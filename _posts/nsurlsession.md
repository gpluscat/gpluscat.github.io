---
title:  NSURLSession
date:   2015-05-07 19:07:00
categories: 
- iOS
tags:
- NSURLSession
---
```
NSString *str5 = @"http://218.92.221.64/ws.cdn.baidupcs.com/file/773936ba7df1284e13da3bf60eef05a6?bkt=p2-qd-46&xcode=cd7a305cad454887d5b17e4e976baa49618ec0c8b3239908a271ffda32bcb45b&fid=4016074960-250528-890821225867280&time=1431064535&sign=FDTAXERLBH-DCb740ccc5511e5e8fedcff06b081203-NZ1c6a0JqUhVJL2z4sDsvFD4Sk4%3D&to=cb&fm=Nan,B,T,ny&sta_dx=606&sta_cs=32&sta_ft=rar&sta_ct=7&newver=1&newfm=1&flow_ver=3&sl=70844495&expires=8h&rt=pr&r=604774147&mlogid=1558529649&vuk=4016074960&vbdid=726484335&fin=office2007.rar&fn=office2007.rar&slt=pm&uta=0&wshc_tag=0&wsts_tag=554c4fd8&wsid_tag=6551459d&wsiphost=ipdbm";
    NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:str5]];
    NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:nil];
    NSURLSessionDownloadTask *downloadTask = [session downloadTaskWithRequest:request];
    [downloadTask resume];    
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task
  didCompleteWithError:(NSError *)error
  {
    NSLog(@">>>>>>>>>>error %@", error);
  }
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
  {
    float progress = totalBytesWritten * 1.0f /totalBytesExpectedToWrite;
    NSLog(@">>>>>>>>>>progress %f", progress);
  }
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)location
  {
    NSString *path = [location path];
    NSString *dPath = [NSString stringWithFormat:@"%@/Library/Caches/tt.mp4", NSHomeDirectory()];
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSError *error;
    if([fileManager fileExistsAtPath:dPath])
        [fileManager removeItemAtPath:dPath error:nil];
    BOOL isMoved = [fileManager moveItemAtPath:path toPath:dPath error:&error];
    NSLog(@">>>>>>>>>>ttPath %@ isMove %d",dPath, isMoved);
    if(error == nil)
    {
        dispatch_async(dispatch_get_main_queue(), ^{
            [self playWithPath:dPath];
        });
    }
    else
    {
        NSLog(@">>>>>>>>>>error %@", [error localizedDescription]);
    }
  }
```