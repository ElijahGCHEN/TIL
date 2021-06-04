## Methodology
In conducting a simple accumulate operation, we compare the performance between `std::accumulate` and the customized `parallel_accumulate` for 30 datasets starting from size of 10, and each time the size of the next dataset is doubled.

## Sequential algorithm
It's simply the `std::accumulate`.

## Concurrent algorithm
```cpp
template<typename Iterator,typename T>
struct accumulate_block
{
    void operator()(Iterator first,Iterator last,T& result)
    {
        result=std::accumulate(first,last,result);
    }
};
template<typename Iterator,typename T>
T parallel_accumulate(Iterator first,Iterator last,T init)
{
    unsigned long const length=std::distance(first,last);
    if(!length)
        return init;
    unsigned long const min_per_thread=25;
    unsigned long const max_threads=
        (length+min_per_thread-1)/min_per_thread;
    unsigned long const hardware_threads=
        std::thread::hardware_concurrency();
    unsigned long const num_threads=
        std::min(hardware_threads!=0?hardware_threads:2,max_threads);
    unsigned long const block_size=length/num_threads;
    cout << "The number of threads is: " << num_threads << endl;;
    cout << "The size of each block is: " << block_size << endl;;
    std::vector<T> results(num_threads);
    std::vector<std::thread>  threads(num_threads-1);
    Iterator block_start=first;
    for(unsigned long i=0;i<(num_threads-1);++i)
    {
        Iterator block_end=block_start;
        std::advance(block_end,block_size);
        threads[i]=std::thread(
            accumulate_block<Iterator,T>(),
            block_start,block_end,std::ref(results[i]));
        block_start=block_end;
    }
    accumulate_block<Iterator,T>()(
        block_start,last,results[num_threads-1]);

    for(auto& entry: threads)
           entry.join();
    return std::accumulate(results.begin(),results.end(),init);
}
```

## Result

The figure shows that for a relatively small data size (less than 65k), the parallel algorithm takes more time than the sequential algorithm due to the extra bookkeeping overhead. However, along with the increase of the data size, the 8-thread parallel algorithm shows a performance of roughly 3~4 times more efficient than the sequential algorithm.

![Screenshot 2021-06-04 at 5 52 38 PM](https://user-images.githubusercontent.com/78017591/120783495-affa9f00-c55d-11eb-8fc1-3fe24214ef2d.png)


## Can parallelism be used in competitive programming?
No. For three reasons:

1. Concurrent computing is highly hardware-sensitive, meaning there could be no gain on some devices. Additionally, some coding contests block part of system functions such as system calls and disk access.
2. Writing and designing a parallel algorithm could be time-consuming, which is a critical disadvantage in competitive programming.
3. Usually problems in competitive programming requires optimal solution in terms of time complexity (big O), concurrent computing, on the other hand, only provides constant factor speedups (you can't make it exponentially faster, usually it's bounded by the # of cores on the CPU).
