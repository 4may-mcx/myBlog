#### 1. 二分查找

```javascript
var search = function(nums, target) {
    let left = 0;
    let right = nums.length-1;
    while(left <= right){
        let mid = Math.floor((right - left) / 2) + left;	//一定要记得加Math.floor  因为js存在类型的自动转换，会把整型自动转换为浮点数
        if(nums[mid] == target){
            return mid;
        } else if (nums[mid] > target){
            right = mid - 1; 
        } else {
            left = mid + 1;
        }
    }
    return -1;
};
```

