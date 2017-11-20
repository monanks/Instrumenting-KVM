# Instrumenting-KVM
Printing KVM VMX exit statistics in dmesg output

we need to edit vmx_handle_exit() function to calculate statistics of KVM VMX exits.
So for statistics we need following data.
1. total number of cycles for all exits combined
2. total number of exits
3. total number of cycles for each exit occured
4. total number of times each exit occured
5. max cycle value for each exit occured
6. min cycle value for each exit occured
7. avg cycle value for each exit occured
8. struct array containing struct(exit_reason, reason_string) for each exit 

so we need to declare global variables for all saving all above mentioned data.
the value of above data would be larger so that we are using 'long long' data type.
for data 1 and 2 we need single variable and for data 3,4,5 and 6 we need array of max size(65).
data 7 we can calculate at the time of printing.

for printing the statistics we are maintaining a counter 'count'.

in vmx_handle_exit function do following

1. if value of count is 500 => print stats and count=0
2. increase count
3. in if condition (exit_reason < kvm_vmx_max_exit_handlers && kvm_vmx_exit_handlers[exit_reason])
    increase data 2
    increase data 4[exit_reason]
    get current time stamp counter start = rdtsc();
    get return value of kvm_vmx_exit_handlers[exit_reason](vcpu); and save in var temp
    get current time stamp counter end = rdtsc();
    get difference be start and end
    increase data 3[exit_reason] by diff
    increase data 1 by diff
    check max and update
    check min and update
    return temp

declare a print function which runs a for loop and prints all data.
