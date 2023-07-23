# 树莓派安装Nerd Font

## 一、去github上的powerlevel10k的仓库下载字体

`MesloLGS NF Regular.ttf`

## 二、安装字体

- 新建文件夹`/usr/share/fonts/my_fonts`

- 把ttf文件移动到该文件夹底下

  ```bash
  sudo mv Downloads/*.ttf /usr/share/fonts/my_fonts/
  ```

- cd到该目录下

- 执行以下命令

  ```bash
  sudo mkfontscale # 没有该命令的话可通过xfonts-utils来安装
  sudo mkfontdir
  sudo fc-cache
  ```

- 文件夹底下出现`fonts.dir`和`fonts.scale`则说明安装成功
- 也可以执行`fc-list`来验证安装

