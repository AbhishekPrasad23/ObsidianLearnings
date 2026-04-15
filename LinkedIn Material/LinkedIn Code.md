
public class SortMapByKey {  
public static void main(String[] args) {  
Map<String, Integer> unsortedMap = new HashMap<>();  
unsortedMap.put("z", 10);  
unsortedMap.put("b", 5);  
unsortedMap.put("a", 6);  
// Sort by keys in ascending order  
Map<String, Integer> sortedByKeyMap = unsortedMap.entrySet().stream()  
.sorted(Map.Entry.comparingByKey())  
.collect(Collectors.toMap(  
Map.Entry::getKey,  
Map.Entry::getValue,  
(oldValue, newValue) -> oldValue, // Merge function for duplicate keys (not applicable here)  
LinkedHashMap::new // Ensure insertion order is maintained  
));  
System.out.println("Sorted by Key (Ascending): " + sortedByKeyMap);  
}  
}