---
title: Ocean Parcels 工具包无法穿越180°W
date: 2024-04-11 22:01:25
tags: python
---





最近需要修改之前的粒子追踪代码，发现粒子怎么也穿不过180°W。（之前实验的粒子轨迹不涉及西北极（波弗特流涡）那边的事情）

但是想法又变了 哈哈哈哈 这就是我！！ 老变！

查了半天 原来官方教程已经给出了答案……链接如下：

[Periodic boundaries — Parcels v3.0.2-13-gdaa4b06 documentation (oceanparcels.org)](https://docs.oceanparcels.org/en/latest/examples/tutorial_periodic_boundaries.html)

只要加入周期性边界即可

```PYTHON
 # set periodic BC
fieldset.add_constant("halo_west", fieldset.U.grid.lon[0])
fieldset.add_constant("halo_east", fieldset.U.grid.lon[-1])

fieldset.add_periodic_halo(zonal=True)

def periodicBC(particle, fieldset, time):
    if particle.lon < fieldset.halo_west:
        particle_dlon += fieldset.halo_east - fieldset.halo_west
    elif particle.lon > fieldset.halo_east:
        particle_dlon -= fieldset.halo_east - fieldset.halo_west
```

将上述代码加在`pset.execute`之前就行，并且要在kernels里面加入`periodicBC`

ps: 教程上说需要满足：`fieldset.U.lon[:, 0] >= fieldset.U.lon[:, -1]`

但是我的情况是反过来的，所以就改了官方的代码。

![image-20240411220928822](https://cdn.jsdelivr.net/gh/jiaoyueLIB/images@main/img/image-20240411220928822.png)

记录一下~
