## 并查集

https://blog.csdn.net/qq_41754350/article/details/81271567

https://zhuanlan.zhihu.com/p/93647900/

https://blog.csdn.net/dm_vincent/article/details/7655764 （最全面）（还没看）

## Bellman-Ford算法

https://www.jianshu.com/p/b876fe9b2338

## 快速幂

https://blog.csdn.net/qq_19782019/article/details/85621386

- 不是最优，但最直观的实现方式

> 1. **long** **long** **fastPower**(**long** **long** base, **long** **long** power) {
> 2. ​    **long** **long** result = 1;
> 3. ​    **while** (power > 0) {
> 4. ​        **if** (power % 2 == 0) {
> 5. ​            //如果指数为偶数
> 6. ​            power = power / 2;//把指数缩小为一半
> 7. ​            base = base * base % 1000;//底数变大成原来的平方
> 8. ​        } **else** {
> 9. ​            //如果指数为奇数
> 10. ​            power = power - 1;//把指数减去1，使其变成一个偶数
> 11. ​            result = result * base % 1000;//此时记得要把指数为奇数时分离出来的底数的一次方收集好
> 12. ​            power = power / 2;//此时指数为偶数，可以继续执行操作
> 13. ​            base = base * base % 1000;
> 14. ​        }
> 15. ​    }
> 16. ​    **return** result;
> 17. }

## 归并排序

- 自己写的
- 看的时候到vscode看比较舒服

> #include <iostream>
>
> #include <ctime>
>
> #include <stdlib.h>
>
> using namespace std;
>
> int n=10;
>
> int a[10];
>
> void merge_sort(int l,int r){
>
>   if (l==r) return;
>
>   int mid=(r-l)/2+l;
>
>   merge_sort(l,mid);
>
>   merge_sort(mid+1,r);
>
>   */**
>
>   if tmp's size is "nn"(=r-l+1)
>
>   the merge (from tmp[] to a[]) needs to solve the mismatch of two indexes
>
>   so the eazy way is to let the tmp.size() be "n" rather than "nn"
>
>   */
>
>   //int nn=r-l+1;
>
>   int tmp[n];
>
>   int l_index=l,r_index=mid+1,tmp_index=l;
>
>   while(l_index<=mid && r_index<=r){
>
> ​    if (a[l_index]<a[r_index]){
>
> ​      tmp[tmp_index++]=a[l_index];
>
> ​      l_index++;
>
> ​    }
>
> ​    else{
>
> ​      tmp[tmp_index++]=a[r_index];
>
> ​      r_index++;
>
> ​    }
>
>   }
>
>   while(l_index<=mid){
>
> ​    tmp[tmp_index++]=a[l_index];
>
> ​    l_index++;
>
>   }
>
>   while(r_index<=r){
>
> ​    tmp[tmp_index++]=a[r_index];
>
> ​    r_index++;
>
>   }
>
>   for (int i=l;i<=r;++i){
>
> ​    a[i]=tmp[i];
>
>   }
>
> }
>
> int main(){
>
>   srand(time(0));
>
>   for (int i=0;i<n;++i){
>
> ​    a[i]=rand()%100;
>
> ​    cout<<a[i]<<' ';
>
>   }
>
>   cout<<endl;
>
>   merge_sort(0,n-1);
>
>   for (int i=0;i<n;++i){
>
> ​    cout<<a[i]<<' ';
>
>   }
>
>   cout<<endl;
>
>   return 0;
>
> }

## 快排

> void quick_sort(int a[],int l,int r){
>
>   if (l==r) return;
>
>   int pivot=a[l];
>
>   int l_index=l,r_index=r;
>
>   while(l_index<r_index){
>
> ​    while(l_index<r_index && a[r_index]>pivot) r_index--;
>
> ​    a[l_index]=a[r_index];
>
> ​    while(l_index<r_index && a[l_index]<pivot) l_index++;
>
> ​    a[r_index]=a[l_index];
>
>   }
>
>   a[l_index]=pivot;
>
>   if (l<=l_index-1)
>
> ​    quick_sort(l,l_index-1);
>
>   if (l_index+1<=r)
>
> ​    quick_sort(l_index+1,r);
>
> }

## Floyd-Wallshell 算法（求无负回路图中任意两点间最短距离）

> https://www.cnblogs.com/wangyuliang/p/9216365.html

