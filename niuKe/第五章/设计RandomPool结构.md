### 题目
> 设计一种结构, 在该结构中有如下三个功能 :   
insert(key) : 将某个key加入到该结构, 做到不重复加入.  
delete(key) : 将原本在结构中的某个key移除.  
getRandom() : 等概率随机返回结构中的任一个key.  
要求 : insert, delete, getRandom方法的时间复杂度都是O(1).

- 我们设计的RandomPool结构有两个哈希表和整形参数size(初始为0), 一个作为key-index表, 一个是index-key表. 
- insert : 分别在两张表中插入, 插入后size加1. 
- getRandom : use`Math.random`function to get random index from 0 ~ size-1, and return the key which search in indexKeyMap by randomIndex. 
- delete : It's the most important part of this question.  Our idea is to move the last row of the recond to delete recond. and then delete the deleteKey from two Map. 
- Why are we doing this ? 如果我们的RandPool结构不需要进行删除操作, 我们在进行getRandom操作的时候就可以直接调用Math.random函数产生随机的index值, 这是因为目前的index值是连续的, 我们不用担心会出现空洞. 
- 如果删除的时候我们直接在哈希表中进行删除, 会造成index的值不连续, 那么我们的getRandom方法将无法使用. 如果情况更加极端:有1000个key, 我们进行了999次删除, 剩下1个key了. 这时我们进行getRandom的话还要去查这个数是否被删除了, 时间复杂度就远不止O(1). 
- 所以, 我们的方法是 : 把最后一条key, 放到被删除的key的位置, 然后size减1, 相当于删除了key, 但是保持了index索引的连续性. **这个方法很常用**


> The code is as follow:

```
public class RandomPool {
    public static class Pool<K>{
        private HashMap<K, Integer> keyIndexMap;
        private HashMap<Integer, K> indexKeyMap;
        private int size;

        public Pool(){
            this.keyIndexMap = new HashMap<K, Integer>();
            this.indexKeyMap = new HashMap<Integer, K>();
            this.size = 0;
        }

        public void insert(K key){
            if (!this.keyIndexMap.containsKey(key)){
                this.keyIndexMap.put(key, this.size);
                this.indexKeyMap.put(this.size++, key);
            }
        }

        public void delete(K key){
            if (this.keyIndexMap.containsKey(key)){
                int deleteIndex = this.keyIndexMap.get(key);
                int lastIndex = --size;
                K lastKey = this.indexKeyMap.get(lastIndex);
                this.keyIndexMap.put(lastKey, deleteIndex);
                this.indexKeyMap.put(deleteIndex, lastKey);
                this.keyIndexMap.remove(key);
                this.indexKeyMap.remove(lastIndex);
            }
        }

        public K getRandom(){
            if (this.size == 0){
                return null;
            }
            int randomIndex = (int)(Math.random() * this.size); //0 ~ size - 1
            return this.indexKeyMap.get(randomIndex);
        }
    }

    public static void main(String[] args){
        Pool<String> pool = new Pool<>();
        pool.insert("a");
        pool.insert("b");
        pool.insert("c");
        pool.insert("d");
        for(int i = 0; i < 10; i++){
            System.out.print(pool.getRandom() + " ");
        }
        pool.delete("b");
        System.out.println();
        for(int i = 0; i < 10; i++){
            System.out.print(pool.getRandom() + " ");
        }
    }
}
```