# Conda使用

## 1 conda环境的创建、切换和删除

- `conda info -e` 查看conda管理的虚拟环境列表
- `conda create -n env_name ` 创建环境
- `conda activate env_name` 激活环境
- `conda deactivate` 退出环境
- `conda remove -n env_name --all` 删除环境,如果执行指令时正在环境中，则无法顺利执行，需要退出后才执行成功

## 2 conda查找、安装、删除软件包

- `conda list` 查看当前环境下已安装的包
- `conda search package_name` 查找包
- `conda install package_name` 安装包
- `conda remove package_name` 删除包
- `conda update package_name` 更新包