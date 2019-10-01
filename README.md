# summarize-process-sample
Combines similar threads in a macOS process sample or spindump, and outputs a summary.  When debugging massively-multithreaded apps, this could help trim down these reports into something readable.

Usage:

    sample [some-process] | summarize-process-sample

Example output:

    Sampled 236 threads
    
    Stack traces with similar threads grouped:
      125 x Thread_13171144
                      1753 thread_routine(void*)  (in libzmq.dylib) + 48  [0x125303e3a]
                        1753 zmq::kqueue_t::loop()  (in libzmq.dylib) + 165  [0x1252f6ba1]
                          1753 kevent  (in libsystem_kernel.dylib) + 10  [0x7fff5ee19716]
        9 x Thread_13173137
                    1753 __workq_kernreturn  (in libsystem_kernel.dylib) + 10  [0x7fff5ee14b6a]
        6 x Thread_13171065: MIO Mounting Thread
                      1753 __NSThread__start__  (in Foundation) + 1218  [0x7fff33de4104]
                        1753 -[PluginLockPair scanPaths]  (in MIO) + 484  [0x1076bfa14]
                          1753 -[NSConditionLock lockWhenCondition:beforeDate:]  (in Foundation) + 91  [0x7fff33e52a14]
                            1753 -[NSCondition waitUntilDate:]  (in Foundation) + 129  [0x7fff33e2fa56]
                              1753 _pthread_cond_wait  (in libsystem_pthread.dylib) + 775  [0x7fff5eed05c6]
        6 x Thread_13171058: com.apple.helium-unit.2.0
                      1753 hg_pthread_loop(void*)  (in Helium) + 187  [0x1089d206b]
                        1753 _pthread_cond_wait  (in libsystem_pthread.dylib) + 724  [0x7fff5eed0593]
        3 x Thread_13171134: com.apple.helium.rq.pbo-rbu0.vs0
                      1753 StartPBOExecUnitFunc(void*)  (in Helium) + 14  [0x10897fa2e]
                        1753 HGPBOReadbackExecUnit::RunLoop()  (in Helium) + 186  [0x10897fafa]
                          1753 HGRenderQueue::GetPBOReadbackJob(HGPBOReadbackExecUnit*, HGPBOReadbackJob**)  (in Helium) + 75  [0x1088aabfb]
                            1753 HGSynchronizable::Wait()  (in Helium) + 51  [0x108a1e2b3]
                              1753 _pthread_cond_wait  (in libsystem_pthread.dylib) + 724  [0x7fff5eed0593]

This indicates that of this process sample's 236 threads, over half of them are individually waiting on kernel events — that's possibly an excessive number of kernel event queues.
