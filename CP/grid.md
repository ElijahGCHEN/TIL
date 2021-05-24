## Initialization
```cpp
int H, W; // width and height

// Using long as the type for element data
```
There are several ways:
1. as 2-d pointer
```cpp
long** grid = new long*[H]{};
for(size_t h = 0; h < H; h++){
  grid[h] = new long[W]{};
}
```
2. As nested vector
```cpp
std::vector<vector<long>> grid(H);
for(std::vector<long> v: grid) {
  v = std::vector<long>(W);
  std::fill_n(v.begin(), v.size(), 0);
}
```

## Iterating diagonally
```cpp
for(size_t i = 0; i < H+W-1; i++){
  for(size_t y = 0; y <= i; y++){
    size_t x = i - y;
    if(x < H && y < W) {
      // operation
    }
  }
}
```

## Permutations from (H, W) to (1, 1)
```cpp
long long dp(long long** matrix,int x, int y) {
    if(x == 0 || y == 0) {
        matrix[x][y] = 1;
        return 1;
    }
    
    if (matrix[x-1][y] != 0 && matrix[x][y-1] != 0 ) {
        matrix[x][y] = matrix[x-1][y] + matrix[x][y-1];
    } else {
        matrix[x][y] = dp(matrix, x-1, y) + dp(matrix, x, y-1);
    }
    
    
    return matrix[x][y];
    
}
```

