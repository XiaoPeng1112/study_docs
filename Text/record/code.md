# 编程算法

1. 给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target 的那 两个 整数，并返回它们的数组下标
    ```
    <!-- 方法一：暴力枚举法 时间复杂度 n的平方（n*n）-->
    <!-- 内层循环从外层循环当前元素的下一个位置开始遍历，检查两个元素的和是否等于target的值，相等则返回下标 -->
    function twoSum(nums, target){
        for(let i = 0; i < nums.length; i++){
            for(let j = i + 1; j < nums.length; j++){
                if(nums[i] + nums[j] === target){
                    return [i,j]
                }
            }
        }
        return []
    }

    <!-- 方法二：哈希表（对象模拟）法 -->
    <!-- 
        遍历数组时，对于当前的数字num，先计算出target - num（即需要的补数），然后检查是否已经在对象中存在。
        如果存在则说明找到了符合条件的一对数字，返回下标即可；如果不存在就把当前数字和其下标存入对象中，
        继续遍历下一个数字 
    -->
    function twoSum(nums,target){
        let obj = {};
        for(let i = 0; i < nums.length; i++){
            let res = target - nums[i]
            if(res in obj){
                return [obj[res], i];
            }
            obj[nums[i]] = i
        }
        return []
    }

    ```