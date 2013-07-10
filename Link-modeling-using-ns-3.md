# Introduction

[ns-3](http://www.nsnam.org/) network simulator has ability to work in so called [real-time/emulation](http://www.nsnam.org/wiki/index.php/Emulation_and_Realtime_Scheduler) mode. In that mode, the simulator can exchange packets in [real-time](http://www.nsnam.org/docs/release/3.17/manual/singlehtml/index.html#realtime) with the outside world. It means, that packets originating from simulated nodes can be processed by a real network. Another possibility is to drive a simulated network with packets from real nodes. These two options are covered by two different net devices: _FdNetDevice_ and _TapBridge_.



