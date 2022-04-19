# Bitmap算法(C语言)
Bitmap算法在处理大量数据是有很大好处，而且可以达到数据去重，节省存储空间的效果。  

例子：  

我们要存储这样的一组数据：  

```json
54 18 23 44 57 29 16 46 47 48 57 57 38 61 29 45 50 21 46 43 19 31 39 13 50 53 43 63 45 18 54 13 45 25 46 35 27 47 14 13 54 58 21 52 40 36 58 23 63 47 60 15 35 28 58 20 15 13 53 26 38 39 18 19
```

数据是杂乱无章的,但是可以看出，这些数据的状态单一，就是存在或者不存在。这正对着二进制的0，1.所以可以用八个8位的2进制数来保存他们，从而形成一个位图（Bitmap）。在位图中每一行每一列的对应位置上对应的存放着一个数据，结构像下面一样。   
``` json
bitmap:
0 1 2 3 4 5 6 7
8 9 10 11 12 13 14 15
16 17 18 19 20 21 22 23
24 25 26 27 28 29 30 31
32 33 34 35 36 37 38 39
40 41 42 43 44 45 46 47
48 49 50 51 52 53 54 55
56 57 58 59 60 61 62 63
```  
第一行存放数值位0-7的元素  

第二行存放数值位8-15的元素  

以此类推.........  

因为前面说到这些数据是杂乱无章的，为了让数据更便于观察我们可以先对其进行排序（对数据顺序没有要求），也可以不排序，但是这里进行了排序。

代码段：  

读入数据：

用0-64的随机数给key赋值，之后在利用冒泡排序法进行排序。最后返回结构体指针b。
```c
//输入原始数据
struct bitmap *input(bitmap *b){
    int l;
    for (int i = 0; i < MAXSIZE; i++){
        b->key[i]=rand() % 51 + 13 ; 
    }
    for (int j = 0; j < MAXSIZE - 1; j++){
        for (int k = 0; k < MAXSIZE - 1 -j; k++){
            if (b->key[k]>b->key[k+1]){
                l=b->key[k];
                b->key[k]=b->key[k+1];
                b->key[k+1]=l;    
            } 
        }
    }
    return b;
}
```
创建Bitmap：

首先是通过一次while循环，计算出给元素在bitmap中的水平位置，并根据while循环的次数的到该元素在bitmap中的垂直方向位置（元素数值减8，当减至小于0时，即循环此次数为高的值，该负数+8为水平位置）。之后因为在最初给bitmap中的值都赋了初始值0，并且最初的bit为10000000，所以直接根据w的值对bit进行移位处理，即得到一个数据在bitmap中的位置，把值赋给相应的bitmap[h],下一次循环则在进行一次按位或运算，如果bitmap[h],已经进行过一次或运算，就在与它进行或运算，记录下下一个的元素的值（进行一次有一次或从而可以记录下每一个在这一行的元素值，有就是1，没有就是0）。如果bitmap[h]还是原始值，就直接与bit进行或运算。直至结束。   

代码段：
```c
//创建出bitmap
struct bitmap *creat_bitmap(bitmap *creat_bit){
    int h,w,j; 
    int k;
    uint bit;
    bit=0200;
    for (int i = 0; i < 8; i++){
        creat_bit->bitmap[i] = 0;
    } //先赋初值 00000000
    //建表
    for (j = 0; j < MAXSIZE; j++){
        w = creat_bit->key[j];  
        k=0;      
        while (w >= 0){
            w=w-8; //确定水平方向位置
            k++;
        }                      //根据数值得到相应的bit
        w=w+8;
        //h=-(-8+w-j)/8; //确定确定数值方向位置
        h=k-1;
        creat_bit->bitmap[h]= creat_bit->bitmap[h] | bit>>w;
    }    
    //输出bitmap    
    for ( int i = 0; i < 8; i++)    {
        printf("bitmap=%d\n",creat_bit->bitmap[i]);
    }
    
    return creat_bit; //返回指针
}
```
查找：

通过读取要查找的元素，用上面的方法计算出这个元素在bitmap中的位置。  

根据h找到bitmap[h],向左移动w位，与10000000进行与运算，判断第一位是否为1，如果为1.就找到了输出位置（与元素值一一对应，所以直接输出j），返回位置，否则没有找到，输出error，返回错误。   

代码段：
```c
//查找
int find(bitmap *find_bit){
    int j,w,h;
    printf("find!\ninput data:");
    scanf("%d",&j); //读入要查找的数据
    if (j >= MAXSIZE){
        printf("ERROR!\n"); //错误处理，超出容量
        return ERROR;
    }
    w=j;
    h=0;
    while (w>=0){
        w=w-8;
        h++; //计算出要查找的数据在bitmap中的位置
    }
    w=w+8;
    h=h-1;
    if (find_bit->bitmap[h]<<w & 128){ //判断该位置在移位后是一还是零
        printf("find!\n");    //是一则输出位置
        printf("postion is:%d\n",j);
        return j;
    }
    else{
        printf("not found\n"); //否则没有找到，不存在
        return ERROR;
    }
```
