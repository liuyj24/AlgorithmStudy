### KMP算法扩展出的题目
#### KMP算法的扩展题
> 更加深入理解KMP算法, 并对该思想进行灵活运用
> 17年京东题 : 假如有abcabc这样的原始字符串, 要求在后面添加字符串, 要求添加完后, 一共有两个原始字符串, 而且两个原始字符串的开头不在同一个位置. 问怎么添加最短? 

- 我们当然可以简单地再添加一个原始字符达到要求  
abcabc++abcabc++, 但是确不如这样添加短abcabc++abc++
- 用KMP算法中求next[]数组的思想很容易解. 
- 我们对原始数组求它的next数组, 但不是求到-最后一个元素, 而是要把最后一个元素的next值也求出来. 
- 这样我们就得到了原字符串的前后缀匹配子串, 添加的时候我们利用KMP算法的思路, 从前匹配子串开始添加, 这样保证了添加后的第二重复字符串在原字符串中重叠的部分最多, 进而达到了添加的字符串最短. 

```
//举个例子:
aabba_____
我们求得原字符串得next数组为[-1, 0, 1, 0, 0, 1]
所以得出原字符串得前后缀匹配长度为1 : a ... a
所以我们添加的时候, 可以省略一个a, 添加abbaa即可. 
```
> show the code

```
public class KMP_problem {

    public static String answer(String str){
        if (str == null || str.length() == 0){
            return "";
        }
        char[] ms = str.toCharArray();
        if (ms.length == 1) {
            return str + str;
        }
        if (ms.length == 2){
            return ms[0] == ms[1] ? str + String.valueOf(ms[1]) : str + str;
        }

        int[] next = getNextArray(ms);
        int endNext = next[ms.length];
        return str + str.substring(endNext);
    }

    private static int[] getNextArray(char[] ms) {
        if (ms.length == 1){
            return new int[]{-1};
        }
        int[] next = new int[ms.length + 1];
        next[0] = -1;
        next[1] = 0;
        int i = 2;
        int cn = 0;
        while(i <= ms.length){
            if (ms[i - 1] == ms[cn]){
                next[i++] = ++cn;
            }else if (cn > 0){
                cn = next[cn];
            }else{
                next[i++] = 0;
            }
        }
        return next;
    }

    public static void main(String[] args){
        String startStr = "aabbaa";
        System.out.println(answer(startStr));
    }
}
```
