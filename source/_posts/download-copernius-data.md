---
title: 下载copernicus marine的数据中遇到的问题
date: 2024-04-22 09:48:56
tags: 
- server
- Python
---

最近在下载哥白尼海洋中心的数据，但是该数据还不支持subset的设置，他们又又又升级了下载工具……（小声逼逼 还不如ftp或者之前那个MOTU）

既然不能支持subset，我只能全下下来然后用cdo裁剪了；

但是 这个数据很要命的是 一下载一整年的数据，就报错：`[Errno 24] Too many open files`。作为普通用户，没有办法像教程中说的改变系统的限制

> 在Linux系统中，可以使用`ulimit`命令来查看和设置文件描述符的限制。  

既如此只能写个循环逐月下载了～python API脚本如下（记得在copernicusmarine的环境下）

```python
# Import modules
import copernicusmarine
from pprint import pprint

# Define parameters to run the extraction
dataset_id_daily = "cmems_mod_glo_phy-all_my_0.25deg_P1D-m"

# Define output storage parameters
output_directory = "/~/download_data/"
# Call the get function to save data
for year in range(1993,2022):
    for month in range(1,13):
        get_result_daily = copernicusmarine.get(
        dataset_id=dataset_id_daily,
        filter="*"+str(year)+str(month).zfill(2)+"*.nc",
        output_directory=output_directory,
        no_directories=True)
        pprint(f"List of saved files: {get_result__daily}")
```

对于每下载一个月都要输入`y`确认的问题

```bash
# 一次
echo yes|[命令] # 输入 yes
echo y|[命令] # 输入 y
# 多次
yes yes|[命令] # 输入 yes
yes y|[命令] # 输入 y

# 例
yes y|python scripts_of_download_data.py 
```

另附 cdo后处理的脚本（`cdo_process.sh`）：

```bash
#!/bin/bash
indir="download_data"

year="1999"

# Define input and output region
region="-180,180,60,90"

echo "begin"
for ((i=1;i<=12;i++)); do
# for ((i=4;i<=12;i++)); do
month=$(printf "%02d" $i)

## Sel uv
out_uvdir="uv_sel_01"
for infile  in ${indir}/*${year}${month}*.nc; do
    outfile="${out_uvdir}/uv_oras5_$(basename ${infile})"
    cdo selname,uo_oras,vo_oras ${infile} ${outfile}
done
echo "Selvariables Finished!!!!"

## Sel Arctic
out_regdir="region_sel_02"
for infile  in ${out_uvdir}/*${year}${month}*.nc; do
    outfile="${out_regdir}/Arc_$(basename ${infile})"
    cdo sellonlatbox,${region} ${infile} ${outfile}
done
echo "Selbox Finished!!!"
rm ${out_uvdir}/*${year}${month}*.nc

## sel depth
dep_range="1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35"
out_depdir="dep_sel_03"
for infile in ${out_regdir}/*${year}${month}*.nc; do
    outfile="${out_depdir}/dep_$(basename ${infile})"
    cdo sellevidx,${dep_range} ${infile} ${outfile}
done
rm ${out_regdir}/*${year}${month}*.nc
echo "sel depth Finished!!!!"

## merge time
out_final_dir='final_data_04'
cdo mergetime ${out_depdir}/*${year}${month}*.nc ${out_final_dir}/Arc_uv_oras5_${year}${month}.nc
rm ${out_depdir}/*${year}${month}*.nc
echo "${year}${month} Mergetime Finished"

#rm ${indir}/*${year}${month}*.nc

done
```

---

还尝试了用wget下载的方法，但是它不支持😮‍💨

```python
import wget
import os

str1 = "https://data.marine.copernicus.eu/product/GLOBAL_MULTIYEAR_PHY_ENS_001_031/files?subdataset=cmems_mod_glo_>
str2 = "cmems_mod_glo_phy-all_my_0.25deg_P1D-m-"
str3 = ".nc"
for year in range(2020,2022):
    for month in range(1, 13):
        for day in range(1, 32):
            url = str1 + "{}".format(year) +"/"+ "{:02d}".format(month)+"/"+ str2+"{}".format(year)+"{:02d}".forma>
            path1 = '~/down_wget_test/'
            file_path = os.path.join(path1)
            if not os.path.exists(file_path):
                os.makedirs(file_path)

            try:
                wget.download(url, out=file_path)
            except:
                print("File:" + str2 + "{0}{1:02d}{2:02d}".format(year, month, day) + str3 + " does not exist")

```

---

查看nc文件的数量（检查一下是不是下载全了）

`find -name "*-1993*.nc" | wc -l`

---

CLI的命令

```bash
copernicusmarine get --dataset-id cmems_mod_glo_phy-all_my_0.25deg_P1D-m -nd --filter "*1997/10/*"
```



---



Conferences：

[too many open files(打开的文件过多)](https://blog.csdn.net/Roy_70/article/details/78423880)

[[Errno 24] Too many open files](https://cloud.baidu.com/article/3269701)

[Linux_shell自动输入y或yes](https://blog.csdn.net/linyisonger/article/details/106469176)

[python wget 批量下载](https://blog.csdn.net/Mluoo/article/details/128557284)

[shell 脚本的循环](https://blog.csdn.net/weixin_44324367/article/details/111312156)

[linux 查看文件数量](https://zhuanlan.zhihu.com/p/377523024)
