# Map 实现原理

## 总结

实现原理超级简单，需知：

- 计算机的存储结构是类数组的，一个一个单元格紧密相连。
- 每一个单元格都有对应的地址，通过地址，可以一步读取到单元格中值。比如数组 a[1]，那么，我们在数组 a 的开始的地址上加上 1
  那么，对于 hash 表(map 属于这一种),只需要：
- 将 key 转为对应的 hash 值（单元格地址）
- 将 value 存入到 hash 对应的单元格中
- 每次读取时，计算 key 对应的 hash 值，从对应的单元格中读取出来。

## 1. Map 实现原理 <a id="map&#x5B9E;&#x73B0;&#x539F;&#x7406;"></a>

**本章不属于基础部分但是面试经常会问到建议学学**

### 1.1.1. 什么是 Map <a id="&#x4EC0;&#x4E48;&#x662F;map"></a>

#### key，value 存储 <a id="key&#xFF0C;value&#x5B58;&#x50A8;"></a>

最通俗的话说 Map 是一种通过 key 来获取 value 的一个数据结构，其底层存储方式为数组，在存储时 key 不能重复，当 key 重复时，value 进行覆盖，我们通过 key 进行 hash 运算（可以简单理解为把 key 转化为一个整形数字）然后对数组的长度取余，得到 key 存储在数组的哪个下标位置，最后将 key 和 value 组装为一个结构体，放入数组下标处，看下图：

```text
    length = len(array) = 4
    hashkey1 = hash(xiaoming) = 4
    index1  = hashkey1% length= 0
    hashkey2 = hash(xiaoli) = 6
    index2  = hashkey2% length= 2
```

#### hash 冲突 <a id="hash&#x51B2;&#x7A81;"></a>

如上图所示，数组一个下标处只能存储一个元素，也就是说一个数组下标只能存储一对 key，value, hashkey\(xiaoming\)=4 占用了下标 0 的位置，假设我们遇到另一个 key，hashkey\(xiaowang\)也是 4，这就是 hash 冲突（不同的 key 经过 hash 之后得到的值一样），那么 key=xiaowang 的怎么存储？ hash 冲突的常见解决方法

开放定址法：也就是说当我们存储一个 key，value 时，发现 hashkey\(key\)的下标已经被别 key 占用，那我们在这个数组中空间中重新找一个没被占用的存储这个冲突的 key，那么没被占用的有很多，找哪个好呢？常见的有线性探测法，线性补偿探测法，随机探测法，这里我们主要说一下线性探测法

线性探测，字面意思就是按照顺序来，从冲突的下标处开始往后探测，到达数组末尾时，从数组开始处探测，直到找到一个空位置存储这个 key，当数组都找不到的情况下回扩容（事实上当数组容量快满的时候就会扩容了）；查找某一个 key 的时候，找到 key 对应的下标，比较 key 是否相等，如果相等直接取出来，否则按照顺寻探测直到碰到一个空位置，说明 key 不存在。如下图：首先存储 key=xiaoming 在下标 0 处，当存储 key=xiaowang 时，hash 冲突了，按照线性探测，存储在下标 1 处，（红色的线是冲突或者下标已经被占用了） 再者 key=xiaozhao 存储在下标 4 处，当存储 key=xiaoliu 是，hash 冲突了，按照线性探测，从头开始，存储在下标 2 处 （黄色的是冲突或者下标已经被占用了）

拉链法：何为拉链，简单理解为链表，当 key 的 hash 冲突时，我们在冲突位置的元素上形成一个链表，通过指针互连接，当查找时，发现 key 冲突，顺着链表一直往下找，直到链表的尾节点，找不到则返回空，如下图：

开放定址（线性探测）和拉链的优缺点

- 由上面可以看出拉链法比线性探测处理简单
- 线性探测查找是会被拉链法会更消耗时间
- 线性探测会更加容易导致扩容，而拉链不会
- 拉链存储了指针，所以空间上会比线性探测占用多一点
- 拉链是动态申请存储空间的，所以更适合链长不确定的

### 1.1.2. Go 中 Map 的使用 <a id="go&#x4E2D;map&#x7684;&#x4F7F;&#x7528;"></a>

直接用代码描述，直观，简单，易理解

```text

var mapInit = map[string]string {"xiaoli":"湖南", "xiaoliu":"天津"}


var mapTemp map[string]string

mapTemp = make(map[string]string,10)

mapTemp["xiaoming"] = "北京"
mapTemp["xiaowang"]= "河北"



v1,ok := mapTemp["xiaoming"]
fmt.Println(ok,v1)

if v2,ok := mapTemp["xiaowang"]; ok{
    fmt.Println(v2)
}

for k,v := range mapTemp{
    fmt.Println(k,v)
}

delete(mapTemp,"xiaoming")

l := len(mapTemp)
fmt.Println(l)
```

看了上面的 map 创建，初始化，增删改查等操作，我们发现 go 的 api 其实挺简单易学的

### 1.1.3. Go 中 Map 的实现原理 <a id="go&#x4E2D;map&#x7684;&#x5B9E;&#x73B0;&#x539F;&#x7406;"></a>

知其然，更得知其所以然，会使用 map 了，多问问为什么，go 底层 map 到底怎么存储呢?接下来我们一探究竟。map 的源码位于 src/runtime/map.go 中 笔者 go 的版本是 1.12 在 go 中，map 同样也是数组存储的的，每个数组下标处存储的是一个 bucket,这个 bucket 的类型见下面代码，每个 bucket 中可以存储 8 个 kv 键值对，当每个 bucket 存储的 kv 对到达 8 个之后，会通过 overflow 指针指向一个新的 bucket，从而形成一个链表,看 bmap 的结构，我想大家应该很纳闷，没看见 kv 的结构和 overflow 指针啊，事实上，这两个结构体并没有显示定义，是通过指针运算进行访问的。

```text
//bucket结构体定义 b就是bucket
type bmap{
    // tophash generally contains the top byte of the hash value
    // for each key  in this bucket. If tophash[0] < minTopHash,
    // tophash[0] is a bucket               evacuation state instead.
    //翻译：top hash通常包含该bucket中每个键的hash值的高八位。
    如果tophash[0]小于mintophash，则tophash[0]为桶疏散状态    //bucketCnt 的初始值是8
    tophash [bucketCnt]uint8
    // Followed by bucketCnt keys and then bucketCnt values.
    // NOTE: packing all the keys together and then all the values together makes the    // code a bit more complicated than alternating key/value/key/value/... but it allows    // us to eliminate padding which would be needed for, e.g., map[int64]int8.// Followed by an overflow pointer.    //翻译：接下来是bucketcnt键，然后是bucketcnt值。
    注意：将所有键打包在一起，然后将所有值打包在一起，    使得代码比交替键/值/键/值/更复杂。但它允许//我们消除可能需要的填充，    例如map[int64]int8./后面跟一个溢出指针}
```

看上面代码以及注释，我们能得到 bucket 中存储的 kv 是这样的，tophash 用来快速查找 key 值是否在该 bucket 中，而不同每次都通过真值进行比较；还有 kv 的存放，为什么不是 k1v1，k2v2..... 而是 k1k2...v1v2...，我们看上面的注释说的 map\[int64\]int8,key 是 int64（8 个字节），value 是 int8（一个字节），kv 的长度不同，如果按照 kv 格式存放，则考虑内存对齐 v 也会占用 int64，而按照后者存储时，8 个 v 刚好占用一个 int64,从这个就可以看出 go 的 map 设计之巧妙。

最后我们分析一下 go 的整体内存结构，阅读一下 map 存储的源码，如下图所示，当往 map 中存储一个 kv 对时，通过 k 获取 hash 值，hash 值的低八位和 bucket 数组长度取余，定位到在数组中的那个下标，hash 值的高八位存储在 bucket 中的 tophash 中，用来快速判断 key 是否存在，key 和 value 的具体值则通过指针运算存储，当一个 bucket 满时，通过 overfolw 指针链接到下一个 bucket。

go 的 map 存储源码如下，省略了一些无关紧要的代码

```text
func mapassign(t *maptype, h *hmap, key unsafe.Pointer) unsafe.Pointer {

    alg := t.key.alg

    hash := alg.hash(key, uintptr(h.hash0))

    if h.buckets == nil {
        h.buckets = newobject(t.bucket)
    }
again:

    bucket := hash & bucketMask(h.B)

    b := (*bmap)(unsafe.Pointer(uintptr(h.buckets) +bucket*uintptr(t.bucketsize)))

    top := tophash(hash)
    var inserti *uint8
    var insertk unsafe.Pointer
    var val unsafe.Pointer
bucketloop:

    for {

        for i := uintptr(0); i < bucketCnt; i++ {

            if b.tophash[i] != top {

                if isEmpty(b.tophash[i]) && inserti == nil {
                    inserti = &b.tophash[i]
                    insertk = add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
                    val = add( unsafe.Pointer(b),
                    dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.valuesize) )
                }

                if b.tophash[i] == emptyRest {
                    break bucketloop
                }
                continue
            }

            k := add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
            if t.indirectkey() {
                k = *((*unsafe.Pointer)(k))
            }

            if !alg.equal(key, k) {
                continue
            }

            if t.needkeyupdate() {
                typedmemmove(t.key, k, key)
            }

            val = add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.valuesize))
            goto done
        }

        ovf := b.overflow(t)
        if ovf == nil {
            break
        }
        b = ovf
    }

    if inserti == nil {

        newb := h.newoverflow(t, b)
        inserti = &newb.tophash[0]
        insertk = add(unsafe.Pointer(newb), dataOffset)
        val = add(insertk, bucketCnt*uintptr(t.keysize))
    }


    if t.indirectkey() {
        kmem := newobject(t.key)
        *(*unsafe.Pointer)(insertk) = kmem
        insertk = kmem
    }
    if t.indirectvalue() {
        vmem := newobject(t.elem)
        *(*unsafe.Pointer)(val) = vmem
    }
    typedmemmove(t.key, insertk, key)

    *inserti = top
    h.count++
    return val
}
```

转自：[https://cloud.tencent.com/developer/article/1468799](https://cloud.tencent.com/developer/article/1468799)
