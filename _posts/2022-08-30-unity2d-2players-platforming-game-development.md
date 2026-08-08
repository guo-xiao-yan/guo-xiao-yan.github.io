---
layout: post
title:  "unity2D双人平台跳跃游戏开发——那些踩过的坑"
date:   2022-08-30 23:58:54 +0800
categories: Game Unity
---

# 声明
我是球球块块工作室的陀螺菌。因具有盈利目的，游戏《球球块块历险记》源码与美术资源、[完整版开发日志](https://qiukuai.yuque.com/staff-hwlox9/efbrqq/zwb61e#wllc)不会公开。本文仅记录开发过程中遇到的一些问题的解决办法。
![我们的游戏](/assets/images/unity2d-2players-platforming-game-development/cover.png)

# 正篇
## 主角动作
### 圆形主角惯性与摩擦力
锁定z轴水平移动，而不是使其滚动。
### 地面判定
在块块左下角与右下角各加一个点，球球下方60度范围内加3个点，防止在平台边缘与斜面上难以跳跃。

```csharp
bool onGround = Physics2D.OverlapCircle(GroundCheck.position, GroundCheckDis, groundLayer);
```

### 防止水面按上无限跳
在水面一小层时使向上速度急剧减小。

```csharp
if (altitude > surfaceDepth && !onGround && verticalMove > 0) {
    rb.velocity = new Vector2(horizontalMove * swimSpeed * Time.fixedDeltaTime, rb.velocity.y / 2);
}
```

### 斜坡变速
添加参数`slopeFactor`，即斜坡对球球速度的影响程度大小。

```csharp
rb.velocity = new Vector2(horizontalMove * (speed * Time.fixedDeltaTime - slopeFactor * rb.velocity.y), rb.velocity.y);
```

让竖直速度在一定程度上影响到水平速度，目前为简单的线性变化，若效果不合适考虑改为其他算法。
### 防止圆形主角跃过坑
掉坑里时加大空气阻力，防止其因速度过快冲过去。
## 存档
### 游戏内存档
在`Game Manager`类中存储，并设置Don't Destroy on Load便可以在场景间传递数据。
角色死亡重开时读档，到达相应Check Point时写入。
### 存档文件读写
序列化与反序列化。
从主菜单进入关卡时读档，从关卡退出时存档。
## 收集物
### 双主角
由于游戏中有两个主角，将脚本挂载至角色上会导致两人数据不互通的后果，故应挂载至收集物上。
### 记录保存
再次加载游戏时需要知道究竟是哪一个已收集哪一个未收集，故使用字典存储而不是仅保存总数。
___
该游戏项目因我要开学了所以暂时搁置。