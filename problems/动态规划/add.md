##题目：给定一个数组arr，选出 n 个数的和等于m
###题目转化：背包问题
即：从一些物品中，取出 N 个，使其恰好装满背包。
###思路：从一个数组得出随机N个数的所有组合，然后遍历得到的组合，判断三个数相加是否等于M。
```
function getCombBySum(array, sum, tolerance, targetCount) {
    /*
    * arr：数据源数组
    * sum：相加的和
    * tolerance：容差，如果不指定此参数，则相加的和必须等于sum参数，指定此参数可以使结果在容差范围内浮动，可选；
    * targetCount：操作数的个数
    */
    let util = {
        /*获取所有的可能组合
        如果是[1,2,3,4,5]取出3个，那么可能性就有10种 C(5,3)= C(5,2)
        公式：
        全排列  P(n,m)=n!/(n-m)!
        组合排列 P(n,m)=n!/m!/(n-m)!
        C(5,2)=5!/2!*3!=5*4*3*2*1/[(2*1)*(3*2*1)]=10
        这是使用了循环加递归做出了组合排序
        */
        getCombination: function(arr, num) { // 索引数组，操作数数量
            const result = [];
            (function f(t, a, n) {
                if(n === 0) return result.push(t);
                for(let i = 0, l = a.length; i <= l - n; i ++) {
                    f(t.concat(a[i]), a.slice(i+1), n - 1); // 递归是在for循环里面。所以当你进入到for循环后。是先执行递归的，直到这个递归有结果返回，你才回到for循环继续执行。
                }
            })([], arr, num);
            return result;
        },
        // 获取数组的索引
        getArrayIndex: function(array) {
            let r = [];
            for(let i = 0; i < array.length; i++) {
                r.push(i);
            }
            return r;
        }
    },
        logic = {
            //  对数组进行排序
            //  获取数组中比sum小的数
            init: function(array, num) {
                const _array = array.concat(), // clone array
                      r = [];
                _array.sort(function(a, b) {
                    return a - b;
                })
                // 当它小于或等于总和时获得所有数字
                for(i = 0;i<_array.length;i++){
                    if(_array[i] <= sum){
                        r.push(_array[i]);
                    }else{
                        break;
                    }
                }
                return r;
            },
            core: function(array,sum,arrayIndex,count,r) {
                let combArray = [],
                    _cca = [],
                    _sum = 0,
                    _cache = [];
                if(count == _returnMark){
                    return;
                }
                // 获取当前的计数总和
                // 这里排序的不是原来的数组,而是求的索引后的数组
                combArray = util.getCombination(arrayIndex, count);
                for(let i = 0; i < combArray.length; i++) {
                    _cca = combArray[i];
                    _sum = 0;
                    _cache = [];
                    for(let k = 0; k < _cca.length; k++) {
                        _sum += array[_cca[k]];
                        _cache.push(array[_cca[k]]);
                    }
                    if(Math.abs(_sum-sum) <= _tolerance){
                        r.push(_cache);
                    }
                }
                logic.core(array,sum,arrayIndex,count-1,r);
            }
        },
        r = [],
        _arr = [],
        _targetCount = 0,
        _tolerance = 0,
        _returnMark = 0;

         // check data
        _targetCount = targetCount || _targetCount;
        _tolerance = tolerance || _tolerance;

        _arr = logic.init(array,sum);
        if(_targetCount){
            _returnMark = _targetCount-1;
        }

        logic.core(_arr,sum,util.getArrayIndex(_arr),(_targetCount || _arr.length),r);

        return r;
}
```