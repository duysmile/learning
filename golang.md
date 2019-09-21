# learn-golang
1. package
2. import
3. function: func
4. multiple result from function
5. variable define: 'var i int' or ':='
6. basic types: bool, string, int ..., float ..., complex ...
7. constants: const
8. for (only for loop)
9. if else
10. switch
11. Defer (will execute after function return, each defer will be pushed onto stack and get by last-in-first-out)
12. Pointers: & -> address, * -> value, *int
13. Structs: 
  > StructExample {
      X, Y int
    }
14. Arrays: var arr [10]int -> array cannot be resized
15. Slice: a[low : high] -> This selects a half-open range which includes the first element, but excludes the last one.
16. Slice are like references to arrays
17. The zero value of a slice is `nil`
18. Creating a slice with make. Eg: 
  > a := make([]int, 5)
 19. Append to a slice
  > var s []int
	> s = append(s, 0)
 20. range: form of the `for` loop iterates over a slice or map.
  > var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}
  > func main() {
  >  for i, v := range pow {
  >    fmt.Printf("2**%d = %d\n", i, v)
  >  }
  > }
