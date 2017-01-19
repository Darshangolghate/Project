# Project
Taxi aggregrator service
import java.util.*;

class taxi{
	String id = "";
	int time,start,position,destination;
	Boolean isavailable;

	taxi(String taxiName, int taxiPosition){
		id = taxiName;position = taxiPosition;
		time = 0;start = 0;
		isavailable = true;
	}

	public void book(int dst,int t,int s){
		destination = dst;time = t; start = s;
		isavailable = false;
	}

	public void update(int t){
		if(!isavailable){
			if(t>=time+start){
				isavailable = true; position = destination;
			}
		}
	}

}

public class Graph{
	Map<String,Integer> mymap;
	int vertices,tt;
	Integer[][] costs;
	String[] nodes;
	taxi[] taxis;
	Integer[] path;
	Integer[] parents;
	int lasttime = 0;

	
	Graph(){
		mymap = new HashMap<String,Integer>();
		nodes = new String[100];
		vertices = 0;
		path = new Integer[100];
		parents = new Integer[100];
		taxis = new taxi[100];
		costs = new Integer[100][100];
		for(int i=0;i<100;i++){
			for(int j=0;j<100;j++){
				costs[i][j]=0;
			}
		}
	}

	public void insertedge(String src,String dst, int t){
		tt = 0;
		int a,b;
		if(!mymap.containsKey(src)){vertices++;mymap.put(src,vertices); nodes[vertices] = src;}
		if(!mymap.containsKey(dst)){vertices++;mymap.put(dst,vertices); nodes[vertices] = dst;}
		a = mymap.get(src);b = mymap.get(dst);
		costs[a][b] = t;
		costs[b][a] = t;
	}

	public void createtaxi(String taxiName,String taxiPosition){
		if(mymap.containsKey(taxiPosition)){
			int a = mymap.get(taxiPosition);
			taxis[tt] = new taxi(taxiName,a);
			tt++;
		}else{
			System.out.println(taxiPosition + " does not exist");
		}
	}
//Dijkstra
	public void shortestpath(String src){
		int source = mymap.get(src),visits = 0,last;
		for(int i=0;i<100;i++){
			path[i] = 20000;
		}
		Boolean[] visit = new Boolean[vertices+1];
		for(int i=0;i<=vertices;i++){
			visit[i] = false;
		}
		path[source] = 0;visit[source] = true;last = source; parents[source] = source;
		while(visits!=vertices){
			visits++;
			int min = 2000;
			for(int i=1;i<=vertices;i++){
				if(costs[i][last]>0){
					if(path[last] + costs[i][last]<path[i]){
						path[i] = path[last] + costs[i][last];
						parents[i] = last;
					}
				}
			}
			for(int i=1;i<=vertices;i++){
				if(!visit[i]){
					if(path[i]<min){
						min = path[i]; last = i;
					}
				}
			}
			visit[last] = true;
		}
		/*for(int i=1;i<=vertices;i++){
			System.out.println(nodes[i] + " : " + path[i] + " : " + nodes[parents[i]]);
		}*/
	}

	public void serve(String src, String dst,int time){
		if(time<lasttime){System.out.println("Such a thing is not possible until you discover Time travel.");}
		else{
			lasttime = time;
		shortestpath(src);
		System.out.println("Available taxis:");
		int min = 2000,ind = 0;
		for(int i=0;i<tt;i++){
			taxis[i].update(time);
			if(taxis[i].isavailable){
				System.out.print("Path of " + taxis[i].id + ": ");
				int j = taxis[i].position;
				String temp = "";
				//System.out.print("=== " + j + " =====");
				while(parents[j]!=j){
					temp += nodes[j] + ", ";
					j = parents[j];
					//System.out.print("=== " + j + " =====");
				}
				temp += src;
				if(path[taxis[i].position]<min){min = path[taxis[i].position];ind = i;}
				System.out.println(temp + ". time taken is " + path[taxis[i].position] + " units");
			}
		}
		System.out.println("** Choose " + taxis[ind].id + " to service the customer request ***");
		System.out.print("Path of customer: ");
		int pd = mymap.get(dst);
		int j = pd;
		String temp = "";
		while(parents[j]!=j){
			temp += reverse(nodes[j]) + ", ";
			j = parents[j];
		}
		temp = reverse(temp);
		temp = src + temp;
		System.out.println(temp + ". time take is " + path[pd] + " units");
		taxis[ind].book(pd,min+path[pd],time);
		}
	}

	public String reverse(String a){
		String temp = "";
		for(int i=a.length()-1;i>=0;i--){
			temp += a.charAt(i);
		}
		return temp;
	}

	public void taxiposition(int t){
		if(t<lasttime){System.out.println("Such a thing is not possible until you discover Time travel.");}
		else{
			lasttime = t;
			int temp = 0;
			for(int i=0;i<tt;i++){
				taxis[i].update(t);
				if(taxis[i].isavailable){
					temp++;
					System.out.println(taxis[i].id + ": " + nodes[taxis[i].position]);	
				}
			}
			if(temp == 0){
				System.out.println("Please Wait, All taxis are currently booked.");
			}
		}
	}

	public void print(){
		for(int i=1;i<=vertices;i++){
			System.out.print(nodes[i] + " -> ");
			for(int j=1;j<=vertices;j++){
				if(costs[i][j]>0){
					System.out.print(nodes[j] + ", ");
				}
			}
			System.out.println();
		}
	}
}
