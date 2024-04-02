# OpenGL-Template

此项目是一个简化的 OpenGL 工程模板，代码参考自[learnopengl-cn](https://learnopengl-cn.github.io/)。

## 环境搭建

Ubuntu 20.04
```bash
sudo apt install libglfw3-dev libglew3-dev
```

## 编译
```bash
mkdir build
cd build
cmake ..
make -j
```

## 运行
```bash
./build/Simulator
```
或者在**VSCode**的`launch.json`中添加
```bash
"program": "${workspaceFolder}/build/Simulator",
```
