when you create a program, and run it, it became a process, and that processes have purposes to do. it might be simple to cook 1 meal at a time, but that won't scale, or you even want to use more than 1 pan

you want to do many stuff, in programming they try to spawn a thread (rust std thread), while this is ok, imagine that your application grow, and the threads grow, apart from the challenges to manage them, they also consume resources

so multiple thread is scary, is it possible to do it in 1 thread? yes, through asynchronous

have to loop because its single thread, otherwise it will exit

loop vs tokio spawn vs tokio spawn await