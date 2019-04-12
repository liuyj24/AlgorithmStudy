### 题目
> 假设有一个大的树T1, 有一棵树T2, 请判断T1中是否有一棵和T2结构完全一样的树? 

- 树结构我们很难比较是否相同, 字符串倒是可以比较...
- 重点 : 把树序列化, 序列化后两段字符串就可以比较了
- 注意1 : 要把每个节点的数字位数固定, 不然可能出现错误. 
- 注意2 : 一定要理解什么叫结构完全一样的一棵树. 如下图, 以树T1为基准. T2这棵树里面不存在T1树, 因为从根节点1开始往下, 除了2, 3以外还有其他节点.  但是T3中就完全存在一棵T1树的结构. 
![image](8F8BF27289764805A933D8C9BAA6F3D2)

```
public class KMP2 {

    public static class Node{
        public int value;
        public Node left;
        public Node right;

        public Node(int data){
            this.value = data;
        }
    }

    public static boolean isSubTree(Node t1, Node t2){
        if (t1 == null || t2 == null){
            return false;
        }
        String t1Str = serialByPre(t1);
        String t2Str = serialByPre(t2);
        return getIndexOf(t1Str, t2Str);
    }

    private static boolean getIndexOf(String t1Str, String t2Str) {
        if (t1Str == null || t2Str == null || t2Str.length() < 1 || t1Str.length() < t2Str.length()){
            return false;
        }
        char[] str1 = t1Str.toCharArray();
        char[] str2 = t2Str.toCharArray();
        int i1 = 0;
        int i2 = 0;
        int[] next = getNextArray(str2);
        while(i1 < str1.length && i2 < str2.length){
            if (str1[i1] == str2[i2]){
                i1++;
                i2++;
            }else if (i2 == 0){
                i1++;
            }else{
                i2 = next[i2];
            }
        }
        return i2 == str2.length ? true : false;
    }

    private static int[] getNextArray(char[] str) {
        if (str.length == 1){
            return new int[]{-1};
        }
        int[] next = new int[str.length];
        next[0] = -1;
        next[1] = 0;
        int i = 2;
        int cn = 0;
        while(i < str.length){
            if (str[i - 1] == str[cn]){
                next[i++] = ++cn;
            }else if(cn > 0){
                cn = next[cn];
            }else{
                i++;
            }
        }
        return next;
    }

    private static String serialByPre(Node node) {
        if (node == null){
            return "#_";
        }
        String res = node.value + "_";
        res += serialByPre(node.left);
        res += serialByPre(node.right);
        return res;
    }

    public static void main(String[] args) {
        Node t1 = new Node(1);
        t1.left = new Node(2);
        t1.right = new Node(3);
        t1.left.left = new Node(4);
        t1.left.right = new Node(5);
        t1.right.left = new Node(6);
        t1.right.right = new Node(7);
        t1.left.left.right = new Node(8);
        t1.left.right.left = new Node(9);

        Node t2 = new Node(2);
        t2.left = new Node(4);
        t2.left.right = new Node(8);
        t2.right = new Node(5);
        t2.right.left = new Node(9);

        System.out.println(isSubTree(t1, t2));
    }
}
```