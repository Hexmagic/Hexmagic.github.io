# 八皇后问题
## 问题介绍

![](https://bkimg.cdn.bcebos.com/pic/86d6277f9e2f070828387b62726caf99a9014c085432?x-bce-process=image/watermark,image_d2F0ZXIvYmFpa2U5Mg==,g_7,xp_5,yp_5/format,f_auto)
八皇后问题，是一个古老而著名的问题，是回溯算法的典型案例。该问题是国际西洋棋棋手马克斯·贝瑟尔于1848年提出：在8×8格的国际象棋上摆放八个皇后，使其不能互相攻击，即任意两个皇后都不能处于同一行、同一列或同一斜线上，问有多少种摆法。 高斯认为有76种方案。1854年在柏林的象棋杂志上不同的作者发表了40种不同的解，后来有人用图论的方法解出92种结果。计算机发明后，有多种计算机语言可以解决此问题。
## 解题思路
八皇后的目标是在8x8的棋盘上(坐标系)上摆放8个皇后(选取8个点)，使其不能互相攻击(8个点中任意一点不与其他点同行同列同斜线)，暴力的方法是依次64个点里选取8个点，然后判断当前选择是否满足条件，该方法效率太低，我们不予考虑。
本次我们学习使用回溯法解决八皇后问题，主要过程是逐层逐列去摆放棋子，先上代码：
```c++
int place[8];
void queen(int row){
    if(row==8){
        // 已经到达最底层 找到了一种解法
        cnt++;
    }else{
        for(int col=0;col<8;col++){
            place[row]=col;
            if(ok(row)){
                queen(row++);
            }
        }
    }
}
```
place存放每一行皇后摆放的位置，一共8个元素分别代表第一行(place[0])到第八行(place[7])选择摆放的列。第一个判断是判断我们已经放好的皇后个数是否等于8(等于8说明找到了一种方法)，主要来看else部分，在else部分我们循环每一列尝试在当前行(row)的当前列(col)放置皇后(place[row]=col),然后判断已经放置好的位置是否满足约束条件，如果满足就递归尝试放置下一行，不满足则跳过当前列去尝试其他列。

下面我们主要研究怎么来实现ok函数，首先当前行前面已经放置过的位置肯定满足约束(不然也不能到当前这一步)，所以他们之间不需要相互判断，其次当前行的放置肯定和之前的放置不同行(因为row不同)。那么我们这里只需要判断当前行的放置是否和之前的放置是否同列同斜线就行了。
```c++
bool ok(int row){
    for(int i=0;i<row;i++){
        if(palce[row]==place[i]){
            return false;
        }else if(同斜线){
            return false;
        }
    }
    return true;
}
```
上面的代码不完整，因为这里我们还不知道怎么判断同斜线🤷‍♀️，画个图来看看把：

![左斜线](../images/left.png)
![右斜线](../images/right.png)

我们发现处于左斜线的元素的横纵坐标相加的和想同，处于右斜线上的元素纵坐标与横坐标的差相同，例如；
0,2 和1，1处于同一左斜线，（0+2）=（1+1）
0,0和 3，3处于同一右斜线， （3-3）=（0-0）
那么我们补全我们的OK函数，有：
```c++


bool ok(int row){
    for(int i=0;i<row;i++){
        if(palce[row]==place[i]){
            return false;
        }else if(place[row]-row==place[i]-i){
            // 同右斜线
            return false;
        }else if(place[row]+row==place[i]+i){
            //同左斜线
            return false;
        }
    }
    return true;
}
```
## 最终编码
```c++
#include <iostream>
using namespace std;

int place[8];
int cnt;
bool ok(int row)
{
    for (int i = 0; i < row; i++)
    {
        if (place[row] == place[i] || place[row] - row == place[i] - i || place[row] + row == place[i] + i)
        {
            return false;
        }
    }
    return true;
}

void queen(int row)
{
    if (row == 8)
    {
        // 已经到达最底层 找到了一种解法
        cnt++;
    }
    else
    {
        for (int col = 0; col < 8; col++)
        {
            place[row] = col;
            if (ok(row))
            {
                queen(row+1);
            }
        }
    }
}

int main()
{
    queen(0);
    cout << "8 Queee: " << cnt << endl;
}
```
