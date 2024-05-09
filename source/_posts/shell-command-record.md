---

title: Shell 常用命令
date: 2024-04-27 16:50:24
tags:
- server
- Shell
---

# 批量修改文件名

```bash
 for file in `ls ./*_all.py`
do
        rename   '_EEB'  '_WEB'  ${file}
done
```

# 批量修改文件内容（替换）

```bash
 for file in `ls ./*_all.py`
 do
 sed -i 's/AmunEEB/AmunWEB/g'  ${file}
 done
```



---

以下是草稿纸

```bash
nohup python -W ignore Nan_EEB/Nan_EEB_backwards_level1_MM02.py > Nan_EEB/log_info/level1_MM02.log 2>&1 &
nohup python -W ignore Nan_EEB/Nan_EEB_backwards_level1_MM05.py > Nan_EEB/log_info/level1_MM05.log 2>&1 &
nohup python -W ignore Nan_EEB/Nan_EEB_backwards_level1_MM07.py > Nan_EEB/log_info/level1_MM07.log 2>&1 &
nohup python -W ignore Nan_EEB/Nan_EEB_backwards_level1_MM10.py > Nan_EEB/log_info/level1_MM10.log 2>&1 &
```

```bash
nohup python -W ignore Nan_WEB/Nan_WEB_backwards_level1_MM02.py > Nan_WEB/log_info/level1_MM02.log 2>&1 &
nohup python -W ignore Nan_WEB/Nan_WEB_backwards_level1_MM05.py > Nan_WEB/log_info/level1_MM05.log 2>&1 &
nohup python -W ignore Nan_WEB/Nan_WEB_backwards_level1_MM07.py > Nan_WEB/log_info/level1_MM07.log 2>&1 &
nohup python -W ignore Nan_WEB/Nan_WEB_backwards_level1_MM10.py > Nan_WEB/log_info/level1_MM10.log 2>&1 &
```



```bash
nohup python -W ignore Aum_EEB/Aum_EEB_backwards_level26_all.py > Aum_EEB/log_info/level26.log 2>&1 &
nohup python -W ignore Aum_WEB/Aum_WEB_backwards_level26_all.py > Aum_WEB/log_info/level26.log 2>&1 &
nohup python -W ignore Nan_EEB/Nan_EEB_backwards_level26_all.py > Nan_EEB/log_info/level26.log 2>&1 &
nohup python -W ignore Nan_WEB/Nan_WEB_backwards_level26_all.py > Nan_WEB/log_info/level26.log 2>&1 &

```

cdo 重采样

```
cdo remapnn,grid.txt infile.nc outfile.nc
```

the content of `grid.txt` is as following:

```
gridtype = lonlat
xsize = 1440
ysize = 240
xfirst = -180
xinc = 0.25
yfirst = 60
yinc = 0.125
```

```
#!/bin/bash
indir="vomecrtn"

echo "begin"
## remap
out_uvdir="remap_files/vomecrtn"
for infile  in ${indir}/*.nc; do
    outfile="${out_uvdir}/lonlatbox_$(basename ${infile})"
    cdo remapnn,grid.txt ${infile} ${outfile}
done
echo "remap Finished!!!!"
```

