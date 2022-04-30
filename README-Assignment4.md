Assignment 4: Nested Paging vs Shadow Paging

Team - 
1. Neelakanta Bhagavatula (SID: 015261909)
2. Rushabh Mehta (SID: 015470585)

Individual Contributions:

Neelakanta -
  1. Inserted KVM module with EPT diabled (Nested Paging).
  2. Analysed total number of exits for different exit types.

Rushabh Mehta -
  1. Inserted KVM module with EPT enabled (Shadow Paging).
  2. Analysed total number of exits for different exit types.

Steps followed for Assignment 4:
  1. Analysed total number of exits for each type of exit handled by KVM.
  2. Shut down the inner VM.
  3. Removed kvm-intel module usine "sudo rmmod kvm-intel" command.
  4. Loaded kvm-intel module with ept disabled using "sudo insmod /lib/modules/5.17.0+/kernel/arch/x86/kvm/kvm-intl.ko ept=0" command.
  5. Booted up inner VM and analysed the number of exits for each type of exit handled by KVM.

Output Screenshots

Below are results with EPT enabled

![vm1](https://user-images.githubusercontent.com/98799930/166089949-91ac4c48-2e34-4353-be7e-a1f8b6c7607d.PNG)
![vm2](https://user-images.githubusercontent.com/98799930/166089951-1c7cb5b2-4fca-4d3f-891c-cf827b49f41a.PNG)
![vm3](https://user-images.githubusercontent.com/98799930/166089953-f2dceea2-599c-43d9-b299-6cdaa2ee1439.PNG)
![vm4](https://user-images.githubusercontent.com/98799930/166089956-f1cd157c-9cec-4774-8615-ee82fa6e0f1f.PNG)
![vm5](https://user-images.githubusercontent.com/98799930/166089957-0176ee4f-37cf-4a71-bc59-2f1c6b7743a1.PNG)
![vm6](https://user-images.githubusercontent.com/98799930/166089960-b2849fe1-1592-4a55-b2cc-5cf5ead020e3.PNG)

Below are results with EPT disabled

![vm7](https://user-images.githubusercontent.com/98799930/166089974-2267175a-eff2-460b-a678-2b317b4efb7a.PNG)
![vm8](https://user-images.githubusercontent.com/98799930/166089976-9c755455-b5f7-40c4-945b-523b5119434b.PNG)
![vm9](https://user-images.githubusercontent.com/98799930/166089977-51a99c40-d6e1-4e4f-b0ee-276ded553818.PNG)
![vm10](https://user-images.githubusercontent.com/98799930/166089978-22f9443c-acf2-4562-b502-1e2334e1ab5f.PNG)
![vm11](https://user-images.githubusercontent.com/98799930/166089979-8a5378e7-28f2-4ea7-bdb4-0729cde8f7aa.PNG)

Regarding exit counts -
  The total number of exits were more with EPT disabled. The expected exits were few hundred's more but it turned out to be very much greater then expected.
  
What changed between two runs?
  The number of exits handled by KVM changed between both runs. Because shadow paging requires page fault to be enabled which inturn results in more number of exits as it causes TLB flush, change in CR3.
