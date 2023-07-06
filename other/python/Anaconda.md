Anaconda，中文大蟒蛇，是一个开源的Python发行版本，其包含了conda、Python等180多个科学包及其依赖项。

conda --version
创建环境：conda create --name <env_name> <package_names>
显示所有环境：conda info --envs     /conda env list
切换环境：conda activate sheepSovler
退出环境：conda deactivate
复制环境：conda create --name <new_env_name> --clone <copied_env_name>
删除环境：conda remove --name <env_name> --all
搜索包(精确查找)：conda search --full-name <package_full_name>
搜索包(模糊查找)：conda search <text>
获取当前环境中已安装的包信息：conda list
在指定环境中安装包：conda install --name <env_name> <package_name>
在当前环境中安装包：conda install <package_name>
卸载指定环境中的包：conda remove --name <env_name> <package_name>
卸载当前环境中的包：conda remove <package_name>
更新所有包：conda update --all
更新指定包：conda update <package_name>







