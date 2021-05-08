# delete
>The "delete" mechanism can be used for any function. For example, we can eliminate an undesired conversion like this:
>```cpp
>  struct Z {
>    // ...
>    Z(long long);     // can initialize with an long long   
>    Z(long) = delete; // but not anything less
>  };
>```


# explicit
>Prefixing the **explicit** keyword to the constructor prevents the compiler from using that constructor for implicit conversions.
