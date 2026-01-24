# python-project
import heapq,time
class PunjabRouteMap:
    def __init__(self):
        self.graph={}
    def add_road(self,a,b,d):
        a=a.lower();b=b.lower()
        self.graph.setdefault(a,[]).append((b,d))
        self.graph.setdefault(b,[]).append((a,d))
    def shortest_path(self,s,e):
        s=s.lower();e=e.lower()
        pq=[(0,s,[])]
        vis=set()
        while pq:
            dist,city,path=heapq.heappop(pq)
            if city in vis:continue
            vis.add(city)
            path=path+[city]
            if city==e:return dist,path
            for n,w in self.graph.get(city,[]):
                if n not in vis:
                    heapq.heappush(pq,(dist+w,n,path))
        return None,None

def track_car(path,graph):
    seg=[]
    for i in range(len(path)-1):
        for n,w in graph[path[i]]:
            if n==path[i+1]:seg.append(w)
    total=sum(seg);remain=total;covered=0
    speed=60
    print("\nTracking Started")
    print("Total Distance:",total,"km")
    for d in seg:
        while d>0:
            step=10 if d>=10 else d
            d-=step;covered+=step;remain-=step
            time_hr=remain/speed
            h=int(time_hr)
            m=int((time_hr-h)*60)
            print("Covered:",covered,"km | Remaining:",remain,"km | ETA:",h,"hour",m,"min")
            time.sleep(0.1)
    print("Destination Reached\n")

class RouteManager:
    def __init__(self):
        self.routes=[]
    def insert(self,s,e,d,p):
        self.routes.append((s,e,d,p))
        print("Route Inserted Successfully")
    def search(self,s,e):
        for r in self.routes:
            if r[0]==s and r[1]==e:return r
        return None
    def delete(self,s,e):
        for i,r in enumerate(self.routes):
            if r[0]==s and r[1]==e:
                self.routes.pop(i);print("Route Deleted");return
        print("Route Not Found")
    def display(self):
        if not self.routes:
            print("No Saved Routes");return
        for i,r in enumerate(self.routes,1):
            print(i,r[0].title(),"->",r[1].title(),"|",r[2],"km |","->".join([x.title() for x in r[3]]))

def main():
    m=PunjabRouteMap();rm=RouteManager()
    m.add_road("Muridke","Sheikhupura",25)
    m.add_road("Sheikhupura","Lahore",15)
    m.add_road("Muridke","Lahore",50)
    m.add_road("Muridke","Gujranwala",35)
    m.add_road("Gujranwala","Lahore",60)
    while True:
        print("=== Car Tracking & Route Optimization ===")
        print("1.Insert new route")
        print("2.Search a route")
        print("3.Delete a route")
        print("4.Display all saved routes")
        print("5.Find & track shortest path")
        print("6.Exit")
        ch=input("Enter your choice:")
        if ch=="1":
            s=input("Enter starting city:").lower()
            e=input("Enter destination city:").lower()
            if s not in m.graph or e not in m.graph:
                print("City not found");continue
            d,p=m.shortest_path(s,e)
            if p:rm.insert(s,e,d,p)
            else:print("No route found")
        elif ch=="2":
            s=input("Enter starting city to search:").lower()
            e=input("Enter destination city to search:").lower()
            r=rm.search(s,e)
            if r:
                print("Route Found:",r[0],"->",r[1],"|",r[2],"km")
            else:print("Route not found")
        elif ch=="3":
            s=input("Enter starting city:").lower()
            e=input("Enter destination city:").lower()
            rm.delete(s,e)
        elif ch=="4":
            rm.display()
        elif ch=="5":
            s=input("Enter starting city:").lower()
            e=input("Enter destination city:").lower()
            if s not in m.graph or e not in m.graph:
                print("City not found");continue
            d,p=m.shortest_path(s,e)
            if p:
                print("Shortest Route:","->".join([x.title() for x in p]))
                print("Total Distance:",d,"km")
                track_car(p,m.graph)
            else:print("No route found")
        elif ch=="6":
            print("Program Exited Successfully");break
        else:print("Invalid Choice")

main()

