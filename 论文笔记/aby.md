<img src="assets\aby\image-20230721163019984.png" alt="image-20230721163019984" style="zoom: 67%;" />

<img src="assets\aby\image-20230722154214419.png" alt="image-20230722154214419" style="zoom: 50%;" />

<img src="assets\aby\image-20230723000616027.png" alt="image-20230723000616027" style="zoom: 67%;" />

自己回答一下自己的问题吧。再次仔细读了一下论文，关键点是在“for l-bit values each operation is performed l times in parallel”这句话上。Yao GC的确是针对单bit来生成电路的，但如果是l-bit就并行处理l次。所以，这里的$Shr^Y_0(x_0)$，代表的是对$x_0 = <x>^A_0 \in \mathbb{Z}_{2^l}$的l次处理。