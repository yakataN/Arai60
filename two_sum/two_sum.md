https://leetcode.com/problems/two-sum/description/?envType=problem-list-v2&envId=xo2bgr0r

step1
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int i=0;i<nums.size();i++) {
            for (int j=i+1;j<nums.size();j++) {
                if (nums[i]+nums[j]==target) {
                    return {i, j};
                }
            }
        }
        return {};
    }
};
```

step2
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int first_idx=0;first_idx<nums.size();first_idx++) {
            for (int second_idx=first_idx+1;second_idx<nums.size();second_idx++) {
                if (nums[first_idx]+nums[second_idx]==target) {
                    return {first_idx, second_idx};
                }
            }
        }
        return {};
    }
};
```

課題
競技プログラミングから離れたコードがかけていない。読みやすいコードがよくわかっていない。
