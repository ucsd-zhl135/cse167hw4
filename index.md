I implemented a decent BVH for my raytracer. It builds the hierarchy top down, splitting a node based on the surface area heuristic (i.e. minimizing sum of size of bounding box * number of objects). We will consider all possible divisions on each dimension. Naively, if there's n objects at our node, this can take O(n^2), but it's trivial and obvious to speed this up to O(n log n) by preprocessing prefixes and suffixes. However, that is still slow. Assuming we split evenly, the complexity can be O(n log^2 n), which can be slow if n > 1e5. However, we can see that we don't need to resort at every node, as long as we sort initially, and maintain the ordering for each axis as we partition our objects. This can be done with std::stable_partition. Therefore, the total complexity to build the BVH, if we have n objects in total, can be about O(n log n), assuming we split nodes evenly. In practice, this way is maybe 80% faster than the O(n log^2 n) way. However, the constant factor is still quite high, and it takes around 6 seconds to build a BVH when n ~ 2e6. Probably, this is because the SAH doesn't produce very balanced trees, so we end up with a worse complexity. Indeed, the height of the BVH in a case with n ~ 2e6 is 32, much is much larger than log2(2e6). 

A weakness of this approach is that it isn't easily parallelizable, as it's recursive. However, it should be straightforward to write this process iteratively in a BFS fashion, which can be easily parallelized. 

To test my BVH, I wrote a program to convert Blender scenes to our format. More specifically, I needed to export from Blender in .obj, and then parse .obj and convert into our format. And I was too lazy to implement surface normals, so things look kind of triangely. 

Here's some test scenes:


