Anaconda入门

https://conda.io/projects/conda/en/latest/user-guide/concepts/index.html



创建环境

```text
# 指定python版本为2.7，注意至少需要指定python版本或者要安装的包# 后一种情况下，自动安装最新python版本
conda create -n env_name python=2.7
# 同时安装必要的包
conda create -n env_name numpy matplotlib python=2.7
```

切换环境

```text
# 切换到新环境# linux/Mac下需要使用source activate env_name
activate env_name
#退出环境，也可以使用`activate root`切回root环境
deactivate env_name
```

移除环境

```text
conda remove -n env_name --all
```



列出所有环境

`conda info --envs`

输出当前环境的依赖项

`conda env export > enviroment.yaml`



conda install 和 pip install 有什么区别

