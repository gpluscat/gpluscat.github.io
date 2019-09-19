---
title:  使用UIVisualEffectView实现毛玻璃效果
categories: 
- iOS
---
```
UIBlurEffect *beffect = [UIBlurEffect effectWithStyle:UIBlurEffectStyleLight];
UIVisualEffectView *effectView = [[UIVisualEffectView alloc] initWithEffect:beffect];
[_hscodeView addSubview:effectView];
[effectView mas_makeConstraints:^(MASConstraintMaker *make) {
    make.edges.equalTo(_hscodeView);
}];
effectView.alpha = 0.5f;
```