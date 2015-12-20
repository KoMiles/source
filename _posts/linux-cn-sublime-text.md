title: 完美解决linux下sublime_text无法输入中文
date: 2015-12-21 00:01:54
tags: tool
---
__sublime_text__ 被称为'神器',自然有它的长处,但是在linux系统安装sublime_text 后,你会发现,无法输入中文,本文会解决 sublime_txt 无法输入中文的问题.

测试系统:[Linux Mint 17.2](http://www.linuxmint.com/)
输入法:[搜狗输入法](http://pinyin.sogou.com/linux/)
Sublime Text版本:[Sublime Text 3 Build 3083](http://www.sublimetext.com/3)

关于如何安装输入法和sublime,在这里不做叙述,自行百度.

1. 保存下面的代码为sublime-imfix.c 文件
```
/*
sublime-imfix.c
Use LD_PRELOAD to interpose some function to fix sublime input method support for linux.
By Cjacker Huang

gcc -shared -o libsublime-imfix.so sublime-imfix.c `pkg-config --libs --cflags gtk+-2.0` -fPIC
LD_PRELOAD=./libsublime-imfix.so subl
*/
#include <gtk/gtk.h>
#include <gdk/gdkx.h>
typedef GdkSegment GdkRegionBox;

struct _GdkRegion
{
  long size;
  long numRects;
  GdkRegionBox *rects;
  GdkRegionBox extents;
};

GtkIMContext *local_context;

void
gdk_region_get_clipbox (const GdkRegion *region,
            GdkRectangle    *rectangle)
{
  g_return_if_fail (region != NULL);
  g_return_if_fail (rectangle != NULL);

  rectangle->x = region->extents.x1;
  rectangle->y = region->extents.y1;
  rectangle->width = region->extents.x2 - region->extents.x1;
  rectangle->height = region->extents.y2 - region->extents.y1;
  GdkRectangle rect;
  rect.x = rectangle->x;
  rect.y = rectangle->y;
  rect.width = 0;
  rect.height = rectangle->height;
  //The caret width is 2;
  //Maybe sometimes we will make a mistake, but for most of the time, it should be the caret.
  if(rectangle->width == 2 && GTK_IS_IM_CONTEXT(local_context)) {
        gtk_im_context_set_cursor_location(local_context, rectangle);
  }
}

//this is needed, for example, if you input something in file dialog and return back the edit area
//context will lost, so here we set it again.

static GdkFilterReturn event_filter (GdkXEvent *xevent, GdkEvent *event, gpointer im_context)
{
    XEvent *xev = (XEvent *)xevent;
    if(xev->type == KeyRelease && GTK_IS_IM_CONTEXT(im_context)) {
       GdkWindow * win = g_object_get_data(G_OBJECT(im_context),"window");
       if(GDK_IS_WINDOW(win))
         gtk_im_context_set_client_window(im_context, win);
    }
    return GDK_FILTER_CONTINUE;
}

void gtk_im_context_set_client_window (GtkIMContext *context,
          GdkWindow    *window)
{
  GtkIMContextClass *klass;
  g_return_if_fail (GTK_IS_IM_CONTEXT (context));
  klass = GTK_IM_CONTEXT_GET_CLASS (context);
  if (klass->set_client_window)
    klass->set_client_window (context, window);

  if(!GDK_IS_WINDOW (window))
    return;
  g_object_set_data(G_OBJECT(context),"window",window);
  int width = gdk_window_get_width(window);
  int height = gdk_window_get_height(window);
  if(width != 0 && height !=0) {
    gtk_im_context_focus_in(context);
    local_context = context;
  }
  gdk_window_add_filter (window, event_filter, context);
}
```
2. 安装 C/C++ 的编译环境和 gtk libgtk2.0-dev
```
sudo apt-get install build-essential
sudo apt-get install libgtk2.0-dev
```
3. 编译共享内库
```
gcc -shared -o libsublime-imfix.so sublime-imfix.c `pkg-config --libs --cflags gtk+-2.0` -fPIC
```
4. 设置 LD_PRELOAD 并启动 Sublime Text
```
LD_PRELOAD=./libsublime-imfix.so subl
```
5. 修改 /usr/share/applications/sublime_text.desktop 为
```
[Desktop Entry]
Version=1.0
Type=Application
Name=Sublime Text
GenericName=Text Editor
Comment=Sophisticated text editor for code, markup and prose
Exec=env LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so /opt/sublime_text/sublime_text %F
Terminal=false
MimeType=text/plain;
Icon=sublime-text
Categories=TextEditor;Development;
StartupNotify=true
Actions=Window;Document;

[Desktop Action Window]
Name=New Window
Exec=env LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so /opt/sublime_text/sublime_text -n
OnlyShowIn=Unity;

[Desktop Action Document]
Name=New File
Exec=env LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so /opt/sublime_text/sublime_text --command new_file
OnlyShowIn=Unity;
```
6. 把 libsublime-imfix.so 移动到 /opt/sublime_text/ 中
```
mv libsublime-imfix.so /opt/sublime_text/
```
7. 修改 /usr/bin/subl 为
```
#!/bin/sh

export LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so
exec /opt/sublime_text/sublime_text "$@"

```
8. 重启电脑

到此,就可以在sublime中输入中文,可以愉快的码字啦~

文章参考: [https://www.sinosky.org/linux-sublime-text-fcitx.html](https://www.sinosky.org/linux-sublime-text-fcitx.html)

by komiles 2015-12-21
