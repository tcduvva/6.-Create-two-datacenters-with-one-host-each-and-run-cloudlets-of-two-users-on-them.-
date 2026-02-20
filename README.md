 # Experiment 6  
## Create two datacenters with one host each and run cloudlets of two users on them

---

## Overview
This experiment demonstrates a CloudSim simulation where two users submit cloudlets that execute across two different datacenters. Each datacenter contains one host, and tasks are scheduled using brokers representing users. The experiment shows distributed task execution and resource allocation in a cloud environment.

---

## Aim
To create two datacenters with one host each and execute cloudlets of two different users on them.

---

## Objectives
- Simulate multiple datacenters
- Simulate multiple users
- Configure hosts and resources
- Create VMs for users
- Execute cloudlets
- Observe distributed execution behavior

---

## Tools and Requirements

| Component | Version |
|--------|---------|
| Java | JDK 8+ |
| CloudSim | 3.0.3 |
| IDE | IntelliJ / Eclipse |
| OS | Windows / Linux |

---

## Datacenter Configuration

| Parameter | Value |
|----------|------|
| Datacenters | 2 |
| Hosts per DC | 1 |
| RAM | 2048 MB |
| Storage | 1,000,000 MB |
| Bandwidth | 10000 Mbps |
| Scheduler | Time Shared |

---

## VM Configuration

| VM | MIPS | RAM |
|----|------|-----|
| VM1 | 500 | 512 MB |
| VM2 | 1000 | 512 MB |

---

## Cloudlet Configuration

| Cloudlet | Length |
|----------|--------|
| C1 | 20000 |
| C2 | 40000 |
| C3 | 60000 |
| C4 | 80000 |

---

## Algorithm
1. Initialize CloudSim library  
2. Create two datacenters  
3. Create two brokers (users)  
4. Create VMs for each user  
5. Submit VMs to brokers  
6. Create cloudlets for each user  
7. Submit cloudlets to brokers  
8. Start simulation  
9. Collect results  
10. Stop simulation

 ## Sample Output

========== RESULT ==========
Cloudlet 0 executed in Datacenter 2 using VM 0
Cloudlet 1 executed in Datacenter 3 using VM 1
Cloudlet 2 executed in Datacenter 2 using VM 0
Cloudlet 3 executed in Datacenter 3 using VM 1


## Program 
//TIP To <b>Run</b> code, press <shortcut actionId="Run"/> or
// click the <icon src="AllIcons.Actions.Execute"/> icon in the gutter.
public class Main {
    public static void main(String[] args) {
        //TIP Press <shortcut actionId="ShowIntentionActions"/> with your caret at the highlighted text
        // to see how IntelliJ IDEA suggests fixing it.
        System.out.printf("Hello and welcome!");

        for (int i = 1; i <= 5; i++) {
            //TIP Press <shortcut actionId="Debug"/> to start debugging your code. We have set one <icon src="AllIcons.Debugger.Db_set_breakpoint"/> breakpoint
            // for you, but you can always add more by pressing <shortcut actionId="ToggleLineBreakpoint"/>.
            System.out.println("i = " + i);
        }
    }
}


import org.cloudbus.cloudsim.*;
import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.provisioners.*;
import java.util.*;

public class TwoUserSimulation {

    static Datacenter createDatacenter(String name) {

        List<Host> hostList = new ArrayList<>();

        List<Pe> peList = new ArrayList<>();
        peList.add(new Pe(0,new PeProvisionerSimple(1000)));

        Host host = new Host(
                0,
                new RamProvisionerSimple(2048),
                new BwProvisionerSimple(10000),
                1000000,
                peList,
                new VmSchedulerTimeShared(peList)
        );

        hostList.add(host);

        DatacenterCharacteristics characteristics =
                new DatacenterCharacteristics(
                        "x86","Linux","Xen",
                        hostList,
                        10.0,3.0,0.05,0.001,0.0
                );

        try {
            return new Datacenter(
                    name,
                    characteristics,
                    new VmAllocationPolicySimple(hostList),
                    new LinkedList<Storage>(),
                    0
            );
        }
        catch(Exception e){
            e.printStackTrace();
            return null;
        }
    }

    public static void main(String[] args) {

        try {

            CloudSim.init(2, Calendar.getInstance(), false);

            Datacenter dc1 = createDatacenter("Datacenter_1");
            Datacenter dc2 = createDatacenter("Datacenter_2");

            // USER 1
            DatacenterBroker broker1 = new DatacenterBroker("User1");
            int id1 = broker1.getId();

            Vm vm1 = new Vm(0,id1,500,1,512,1000,10000,"Xen",
                    new CloudletSchedulerTimeShared());

            broker1.submitVmList(Arrays.asList(vm1));

            UtilizationModel model = new UtilizationModelFull();

            Cloudlet c1 = new Cloudlet(0,20000,1,300,300,model,model,model);
            Cloudlet c2 = new Cloudlet(1,40000,1,300,300,model,model,model);

            c1.setUserId(id1);
            c2.setUserId(id1);

            broker1.submitCloudletList(Arrays.asList(c1,c2));


            // USER 2
            DatacenterBroker broker2 = new DatacenterBroker("User2");
            int id2 = broker2.getId();

            Vm vm2 = new Vm(1,id2,500,1,512,1000,10000,"Xen",
                    new CloudletSchedulerTimeShared());

            broker2.submitVmList(Arrays.asList(vm2));

            Cloudlet c3 = new Cloudlet(2,30000,1,300,300,model,model,model);
            Cloudlet c4 = new Cloudlet(3,60000,1,300,300,model,model,model);

            c3.setUserId(id2);
            c4.setUserId(id2);

            broker2.submitCloudletList(Arrays.asList(c3,c4));


            CloudSim.startSimulation();

            List<Cloudlet> r1 = broker1.getCloudletReceivedList();
            List<Cloudlet> r2 = broker2.getCloudletReceivedList();

            CloudSim.stopSimulation();


            System.out.println("\nUSER 1 RESULTS:");
            for(Cloudlet cl : r1)
                System.out.println("Cloudlet "+cl.getCloudletId()+" Time "+cl.getActualCPUTime());

            System.out.println("\nUSER 2 RESULTS:");
            for(Cloudlet cl : r2)
                System.out.println("Cloudlet "+cl.getCloudletId()+" Time "+cl.getActualCPUTime());

        }
        catch(Exception e){
            e.printStackTrace();
        }
    }
}












## Result
Cloudlets from different users were successfully executed across two datacenters. The simulation demonstrates distributed task execution and broker-based scheduling.

---<img width="443" height="172" alt="image" src="https://github.com/user-attachments/assets/212fc9aa-9527-4d17-a2f9-96254503b714" />


## Conclusion
This experiment verifies that CloudSim accurately simulates multi-user and multi-datacenter environments. It demonstrates how distributed infrastructure handles workloads and how resources are allocated efficiently in cloud systems.

---
 
 
