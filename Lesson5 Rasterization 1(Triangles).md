## Lesson5 Rasterization 1(Triangles)	光栅化（三角形的离散化）

### 名词解释

#### field-of-view(FOV) 垂直可视角度

#### aspect ratio 宽高比

![image-20211025223805448](assets\image-20211025223805448.png)

通过切面可以得到
$$
tan(fovY/2) = t/|n|
$$

$$
aspect = r/t
$$

![image-20211025224225843](assets\image-20211025224225843.png)

#### 光栅化

$$
在做完MVP变换之后，所以的物体都会停留在定义好的[-1,1]^3立方体上。画在由一组二位像素组成的屏幕上的过程就叫光栅化
$$



#### 定义屏幕空间

![image-20211025225236770](assets\image-20211025225236770.png)

1. 像素定义的坐标 (x,y)

2. 像素坐标的范围(0,0)到(width-1,heigt-1)

3. 像素(x,y)的中心是(x+0.5,y+0.5)

4. 整个屏幕的范围是(0,0)到(width,heigt)

   因此我们需要将[-1,1]^3中的物体映射到 —>(0,0)到(width,height)的屏幕上

   在不考虑z的情况下，就是将[-1,1]^2 映射成 [0,width]x[0,height]
   $$
   M_{viewport}=
   \left[
   \begin{matrix}
   width/2&0&0&width/2\\
   0&height/2&0&height/2\\
   0&0&1&0\\
   0&0&0&1\\
   \end{matrix}
   \right]\tag{视口变换}
   $$
   

### Rasterization  As 2Dsampling 2D采样

光栅化中重要的一个过程就是判断一个像素与一个三角行的关系；是在三角形内还是在三角形外

![image-20211025230913573](assets\image-20211025230913573.png)

可以判断像素的中心点是否在三角形内

![image-20211025231358058](assets\image-20211025231358058.png)

定义一个二元函数	inside(tri,x,y)
$$
inside(t,x,y)=
\begin{cases}
0& \text{在三角形t外}\\
1& \text{在三角形t内}
\end{cases}
$$

```c
//计算xy点像素的中心位置与三角形的位置关系
for(int x = 0; x < xmax; ++x)
{
	for(int y = 0; y < ymax; ++y)
	{
		image[x][y] = inside(tri,x+0.5,y+0.5);
	}
}
```

##### 如何判断坐标的里外关系

使用叉乘可以判断一个点在三角形的内部或者外部

![image-20211101212823367](assets\image-20211101212823367.png)

以P0 -> P1 -> P2 构建三个向量，分别与P0Q,P1Q.P2Q向量叉乘。如果结果符号相同，则在三角形内部。

##### 使用边界框来减少像素的遍历

![image-20211101213732849](assets\image-20211101213732849.png)

```c
//只遍历覆盖的像素
for(int x = xmin; x < xmax; ++x)
{
	for(int y = ymin; y < ymax; ++y)
	{
		image[x][y] = inside(tri,x+0.5,y+0.5);
	}
}
```

##### 结果锯齿状（Jaggies）

![image-20211101213937632](assets\image-20211101213937632.png)