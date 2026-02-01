
1.Write a program to find the first non-repeating character in a string using streams.  
String str = "abacdbef";  
Optional<Character> firstNonRepeatingChar = str.chars()  
.mapToObj(c -> (char) c)  
.collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting()))  
.entrySet()  
.stream()  
.filter(e -> e.getValue() == 1L)  
.map(Map.Entry::getKey)  
.findFirst();  
  
2.Given a string, Find Maximum Occurring Character in a String:  
String input = "aabbbccccddddddee";  
Optional<Map.Entry<Character, Long>> maxChar =  
input.chars()  
.mapToObj(c -> (char) c)  
.collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))  
.entrySet()  
.stream()  
.max(Map.Entry.comparingByValue());  
  
3.Given a list of strings, write a program to count the number of strings containing a specific character ‘a’ using Java Stream API  
List<String> strings = Arrays.asList("apple", "banana", "orange", "grape");  
char searchChar = 'a';  
long count = [strings.stream](http://strings.stream/)()  
.filter(str -> str.contains(String.valueOf(searchChar)))  
.count();  
  
4.Given an array of integers, find the kth largest element.  
List<Integer> list = Arrays.asList(1, 12, 44, 32, 52, 81, 59, 84, 72, 37);  
int k = 4;  
Integer num = [list.stream](http://list.stream/)().sorted(Comparator.reverseOrder()).limit(k).skip(k - 1).findFirst().orElse(-1);  
  
5.Given a list of strings, find the longest palindrome string.  
List<String> list = List.of("level", "hello", "radar", "world", "madam", "java", "Malayalam");  
String str = [list.stream](http://list.stream/)().filter(s -> new StringBuilder(s).reverse().toString().equalsIgnoreCase(s))  
.max(Comparator.comparingInt(String::length)).orElse("");  
  
6.Find youngest female employee.  
Optional<Employee> youngestEmp = [empList.stream](http://emplist.stream/)().filter(e -> e.getGender() == "F")  
.min(Comparator.comparingInt(Employee::getAge));  
Employee youngestEmployee = youngestEmp.get();  
  
7.Find the department name which has the highest number of employees.  
Map.Entry<String, Long> maxNoOfEmployeesInDept = [empList.stream](http://emplist.stream/)().collect(Collectors.groupingBy(Employee::getDeptName, Collectors.counting())).  
entrySet().stream().max(Map.Entry.comparingByValue()).get();  
  
8.Print list of employee’s second highest record based on department  
System.out.println("Highest second salary dept wise:: \n" + [empList.stream](http://emplist.stream/)().collect(Collectors.groupingBy(Employee::getDeptName,  
Collectors.collectingAndThen(Collectors.toList(),  
list -> [list.stream](http://list.stream/)().sorted(Comparator.comparingDouble(Employee::getSalary).reversed()).skip(1).findFirst()))));