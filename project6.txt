import java.util.HashMap;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.Stack;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.PriorityQueue;

public class Main {

	public static void main(String[] args) {
		Main m = new Main();
		Graph graph = m.new Graph();
		graph.addNodes(new String[]{"A", "B", "C", "D", "E", "F", "G"});
		graph.addEdge("A", "B");
		graph.addEdge("A", "C");
		graph.addEdge("A", "G");
		graph.addEdge("B", "F");
		graph.addEdge("G", "D");
		graph.addEdge("C", "D");
		graph.addEdge("D", "E");
		graph.printGraph();
		String[] path = graph.DFS("A", "E", "alphabetical");
		System.out.println(Arrays.toString(path));
		path = graph.DFS("A", "E", "reverse");
		System.out.println(Arrays.toString(path));
		path = graph.BFS("A", "E", "alphabetical");
		System.out.println(Arrays.toString(path));
		path = graph.BFS("A", "E", "reverse");
		System.out.println(Arrays.toString(path));
		path = graph.shortestPath("A", "E");
		System.out.println(Arrays.toString(path));
		path = graph.shortestPath("A", "A");
		System.out.println(Arrays.toString(path));
		path = graph.shortestPath("A", "H");
		System.out.println(Arrays.toString(path));
		path = graph.shortestPath("A", "C");
		System.out.println(Arrays.toString(path));
		path = graph.secondShortestPath("A", "C");
		System.out.println(Arrays.toString(path));
	}
	/**
	 * The class for the graph. All boolean methods return true if
	 * successful and false if otherwise. Contains no duplicate nodes,
	 * and is undirected and unweighted.
	 * 
	 * @author awmatkov
	 */
	class Graph {
		private HashMap<String, ArrayList<String>> adjacencyList;

		public Graph() {
	    	adjacencyList = new HashMap<>();
	    }

		/**
		 * Adds a node to the graph
		 * @param name node to be added
		 * @return if node was successfully added
		 */
		public boolean addNode(String name) {
			if(adjacencyList.containsKey(name))
	    		return false;
			adjacencyList.put(name, new ArrayList<>());
	    	return true;
	    }
		
		/**
		 * Adds a list of nodes to the graph
		 * @param names nodes to be added
		 * @return if all nodes were successfully added
		 */
	    public boolean addNodes(String[] names) {
	    	boolean bool = true;
	    	for(String name : names) {
	    		if(!addNode(name))
	    			bool = false;
	    	}
	    	return bool;
	    }
	    
	    /**
	     * Adds edge from one node to another
	     * @param from starting node
	     * @param to ending node
	     * @return if edge was successfully added
	     */
	    public boolean addEdge(String from, String to) {
	    	if(!(adjacencyList.containsKey(from)&&adjacencyList.containsKey(to)))
	    		return false;
	    	if(adjacencyList.get(to).contains(from))
	    		return false;
	    	adjacencyList.get(from).add(to);
	    	adjacencyList.get(to).add(from);
	    	return true;
	    }
	    
	    /**
	     * Adds edges from one node to a string of nodes
	     * @param from node to start edges
	     * @param toList nodes where to connect edges
	     * @return if all edges were successfully added
	     */
	    public boolean addEdges(String from, String[] toList) {
	    	boolean bool = true;
	    	for(String to : toList) {
	    		if(!addEdge(from, to))
	    			bool = false;
	    	}
	    	return bool;
	    }
	    
	    /**
	     * Removes a node from the graph, including edges
	     * @param name node to be removed
	     * @return if node was successfully removed
	     */
	    public boolean removeNode(String name) {
	    	if(!adjacencyList.containsKey(name)) {
	    		return false;
	        }
	    	adjacencyList.remove(name);
	    	for(ArrayList<String> neighbors : adjacencyList.values()) {
	    		neighbors.remove(name);
	    	}
	    	return true;
	    }
	    
	    /**
	     * Removes a string of nodes from the graph, including edges
	     * @param nodeList nodes to be removed
	     * @return if all nodes were successfully removed
	     */
	    public boolean removeNodes(String[] nodeList) {
	    	boolean bool = true;
	    	for(String name : nodeList) {
	    		if(!removeNode(name))
	    			bool = false;
	    	}
	    	return bool;
	    }
	    
	    /**
	     * Prints the graph using an adjacency list representation
	     */
	    public void printGraph() {
	        ArrayList<String> nodes = new ArrayList<>(adjacencyList.keySet());
	        Collections.sort(nodes);
	        for(String node : nodes) {
	            ArrayList<String> adj = adjacencyList.get(node);
	            Collections.sort(adj);
	            System.out.print(node + ": ");
	            for(String val : adj) {
	            	System.out.print(val + ", ");
	            }
	            System.out.println();
	        }
	    }
	    
	    /**
	     * Iteratively finds a node in a graph using depth first search
	     * @param from starting node
	     * @param to ending node
	     * @param neighborOrder order the neighboring nodes are checked
	     * @return array representation of the shortest path between
	     * the two nodes
	     */
	    public String[] DFS(String from, String to, String neighborOrder) {
	    	if(!(adjacencyList.containsKey(from)&&adjacencyList.containsKey(to)))
	    		return new String[0];
	    	HashSet<String> visited = new HashSet<>();
	    	HashSet<String> checked = new HashSet<>();
	    	Stack<String> stack = new Stack<>();
	    	ArrayList<String> path = new ArrayList<>();
	    	stack.push(from);
	    	visited.add(from);
	    	while(!stack.isEmpty()) {
	    		String current = stack.pop();
	    		checked.add(current);
	    		path.add(current);
	    		if(current.equals(to))
	    			return path.toArray(new String[0]);
	    		ArrayList<String> neighbors = adjacencyList.get(current);
	    		if(neighborOrder.equals("alphabetical"))
	    			Collections.sort(neighbors, Collections.reverseOrder());
	    		else if(neighborOrder.equals("reverse"))
	    			Collections.sort(neighbors);
	    		boolean deadEnd = true;
	    		for(String neighbor : neighbors) {
	    			if(!visited.contains(neighbor)) {
	    				deadEnd = false;
	    				stack.push(neighbor);
	    				visited.add(neighbor);
	    			}
	    		}
	    		if(deadEnd) {
	    			path.remove(current);
	    			boolean last = true;
	    			while(last) {
	    				String str = path.remove(path.size()-1);
	    				neighbors = adjacencyList.get(str);
	    				for(String neighbor : neighbors) {
	    					if(!checked.contains(neighbor)) {
	    						last = false;
	    						path.add(str);
	    						break;
	    					}
	    				}
	    			}
	    			
	    		}
	    	}
	    	return new String[0];
	    }
	    
	    /**
	     * Iteratively finds a node in a graph using breadth first search.
	     * @param from starting node
	     * @param to ending node
	     * @param neighborOrder order the neighboring nodes are checked
	     * @return array representation of the shortest path between
	     * the two nodes
	     */
	    public String[] BFS(String from, String to, String neighborOrder) {
	    	if(!(adjacencyList.containsKey(from)&&adjacencyList.containsKey(to)))
	    		return new String[0];
	    	HashSet<String> visited = new HashSet<>();
	    	HashSet<String> checked = new HashSet<>();
	    	LinkedList<String> queue = new LinkedList<>();
	    	ArrayList<String> path = new ArrayList<>();
	    	queue.push(from);
	    	visited.add(from);
	    	while(!queue.isEmpty()) {
	    		String current = queue.pop();
	    		checked.add(current);
	    		path.add(current);
	    		if(current.equals(to))
	    			return path.toArray(new String[0]);
	    		ArrayList<String> neighbors = adjacencyList.get(current);
	    		if(neighborOrder.equals("alphabetical"))
	    			Collections.sort(neighbors, Collections.reverseOrder());
	    		else if(neighborOrder.equals("reverse"))
	    			Collections.sort(neighbors);
	    		boolean deadEnd = true;
	    		for(String neighbor : neighbors) {
	    			if(!visited.contains(neighbor)) {
	    				deadEnd = false;
	    				queue.push(neighbor);
	    				visited.add(neighbor);
	    			}
	    		}
	    		if(deadEnd) {
	    			path.remove(current);
	    			boolean last = true;
	    			while(last) {
	    				String str = path.remove(path.size()-1);
	    				neighbors = adjacencyList.get(str);
	    				for(String neighbor : neighbors) {
	    					if(!checked.contains(neighbor)) {
	    						last = false;
	    						path.add(str);
	    						break;
	    					}
	    				}
	    			}
	    			
	    		}
	    	}
	    	return new String[0];
	    }
	    
	    /**
	     * Finds the shortestPath iteratively using djikstra's algorithm.
	     * 
	     * @param from starting node
	     * @param to ending node
	     * @return array representation of the shortest path between
	     * the two nodes
	     */
	    public String[] shortestPath(String from, String to) {
	    	if(!(adjacencyList.containsKey(from)&&adjacencyList.containsKey(to)))
	    		return new String[0];
	    	HashSet<String> visited = new HashSet<>();
	    	HashMap<String, Integer> distances = new HashMap<>();
	    	for(String node : adjacencyList.keySet())
	    		distances.put(node, Integer.MAX_VALUE);
	    	distances.put(from, 0);
	    	HashMap<String, String> previous = new HashMap<>();
	    	PriorityQueue<String> queue = new PriorityQueue<>((node1, node2) -> distances.get(node1) - distances.get(node2));
	    	queue.add(from);
	    	while(!queue.isEmpty()) {
	    		String current = queue.poll();
	    		if(current.equals(to)) {
	    			ArrayList<String> path = new ArrayList<>();
	    			while(previous.containsKey(current)) {
	    				path.add(current);
	    				current = previous.get(current);
	    			}
	    			path.add(from);
	    			Collections.reverse(path);
	    			return path.toArray(new String[0]);
	    		}
	    		ArrayList<String> neighbors = adjacencyList.get(current);
	    		for(String neighbor : neighbors) {
	    			if(!visited.contains(neighbor)) {
	    				int distance = distances.get(current) + 1;
	    				if(distance<distances.get(neighbor)) {
	    					distances.put(neighbor, distance);
	    					previous.put(neighbor, current);
	    					queue.add(neighbor);
	    				}
	    			}
	    		}
	    		visited.add(current);
	    	}
	    	return new String[0];
	    }
	    
	    /**
	     * Uses the shortestPath method to find the second shortest path by
	     * removing the shortest path from the graph
	     * 
	     * @param from starting node
	     * @param to ending node
	     * @return array representation of the shortest path between
	     * the two nodes
	     */
	    public String[] secondShortestPath(String from, String to) {
	    	if(!(adjacencyList.containsKey(from)&&adjacencyList.containsKey(to)))
	    		return new String[0];
	    	String[] firstPath = shortestPath(from, to);
	    	if(firstPath.length!=0) {
	    		Graph graph = new Graph();
	    		for(String node : adjacencyList.keySet())
	    			graph.addNode(node);
	    		for(String fromNode : adjacencyList.keySet()) {
	    			ArrayList<String> neighbors = adjacencyList.get(fromNode);
	    			for(String toNode : neighbors) {
	    				if(!isNodeInPath(firstPath, toNode))
	    					graph.addEdge(fromNode, toNode);
	    			}
	    		}
	    		String[] secondPath = graph.shortestPath(from, to);
	    		return secondPath;
	    	} else {
	    		return new String[0];
	    	}
	    }
	    
	    /**
	     * Helper method for secondShortestPath
	     * 
	     * @param path a path of nodes in the graph
	     * @param node a node
	     * @return if path contains node
	     */
	    private boolean isNodeInPath(String[] path, String node) {
	    	for(String n : path) {
	    		if(n.equals(node)) {
	    			return true;
	    		}
	    	}
	    	return false;
	    }
	}
}
