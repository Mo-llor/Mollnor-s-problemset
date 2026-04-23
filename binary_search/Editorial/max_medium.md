我们可以二分中位数，然后我们可以把 $a[i] < mid$ 的标为-1， $a[i] \geq mid$ 的标为1 这样我们就可以一个数是比当前子数组的中位数小还是大了喵，如果>0 就是 $\leq$ 中位数喵

# 推导是这个喵

对于数组 $a_1,a_2,a_3,a_4 \dots a_n$ 的中位数将其定义为:将其排序成为 $b_1,b_2,\dots,b_n$，满足 $b_1 \leq b_2 \leq b_3 \leq \dots \leq b_n$,则a的中位数是

$$
median(a) = b_{ \left\lfloor \frac{n + 1}{2} \right\rfloor}
$$

- $median(a) \leq x \iff \sum_{i = 1}^{n}[a_i \leq x] \geq \left\lfloor \frac{n + 1}{2} \right\rfloor \iff \sum_{i = 1}^{n}[a_i > x] \leq \left\lfloor \frac{n}{2} \right\rfloor \iff \sum_{i = 1}^{n}[a_i \leq x] \geq \sum_{i = 1}^{n}[a_i > x]$
- $median(a) \geq x \iff \sum_{i = 1}^{n}[a_i \geq x] > \left\lfloor \frac{n}{2} \right\rfloor \iff \sum_{i = 1}^{n}[a_i < x] < \left\lfloor \frac{n + 1}{2} \right\rfloor \iff \sum_{i = 1}^{n}[a_i \geq x] > \sum_{i = 1}^{n}[a_i < x]$ 
