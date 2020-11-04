# 嵌入式arm-linux开发板使LCD显示图片

## 具体实现代码

```C
#include <sys/types.h>//open
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>	//close、read、write
#include <sys/mman.h>//mmap、munmap

#include <stdio.h>
#include <string.h>

int main()
{
	int fd = open("/dev/fb0",O_RDWR);	//打开LCD的设备文件，设置为可读可写
	int *lcd = mmap(NULL,800*480*4,PROT_READ | PROT_WRITE,MAP_SHARED,fd,0);//mmap映射

	int bmpfd = open("./a.bmp",O_RDWR);	//打开图片，设置可读可写

	unsigned char head[54] = {0};	//用于存储读出来的bmp文件信息
	read(bmpfd,head,54);	//读bmp图片的前54个字节

	int kuan = *((int *)&head[18]);	//图片的宽度
	int gao = *((int *)&head[22]);	//图片的高度

	/*打印输出图片的宽高*/
	printf("kuan = %d\n",kuan);
	printf("gao = %d\n",gao);

	int buf[gao][kuan];	//32位的位图
	char tmp_buf[gao*kuan*3];	//24位的位图

	read(bmpfd,tmp_buf,sizeof(tmp_buf));	//读取图片，存入tmp_buf中

	unsigned char r,g,b,a;	//创建中间变量以方便转换
	unsigned char *p = tmp_buf;		//让*p指向tmp_buf数组首位，方便转换
	unsigned int color = 0,x = 0,y = 0;

	/*由于bmp的格式是BGR，驱动文件的是ARGB，故需要转换下*/
	for(y = 0; y<gao; y++)
		for(x=0; x<kuan; x++)
		{
			b = *p++;	//存入b变量
			g = *p++;	//存入g变量
			r = *p++;	//存入r变量
			a = 0;		//不透明
			color = a<<24|r<<16|g<<8|b;	//使用位操作并存入color中
			buf[y][x]=color;	//分别将ARGB加载到各个像素点
			*(lcd+y*800+x)=buf[y][x];	//让像素点显示出来
		}

	close(fd);	//关闭LCD设备文件
	close(bmpfd);	//关闭位图
	munmap(lcd,800*480*4);	//取消映射LCD

	return 0;
}
```

## 提取其中的32位转24位位图的函数

```C

```

