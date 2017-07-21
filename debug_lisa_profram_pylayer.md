***
`
Traceback (most recent call last):

  **File "/home/iva/lisa-caffe-public/examples/LRCN_activity_recognition/sequence_input_layer.py", line 221, in forward
    new_result_data = [None]*len(self.thread_result['data'])**
    
KeyError: 'data'
terminate called after throwing an instance of 'boost::python::error_already_set'
Aborted at 1500429214 (unix time) try "date -d @1500429214" if you are using GNU date 
PC: @     0x7f2a23b8b428 gsignal

SIGABRT (@0x3e80000145f) received by PID 5215 (TID 0x7f2a2521ba80) from PID 5215; stack trace: 
    @     0x7f2a23b8b4b0 (unknown)
    @     0x7f2a23b8b428 gsignal
    @     0x7f2a23b8d02a abort
    @     0x7f2a241c584d __gnu_cxx::__verbose_terminate_handler()
    @     0x7f2a241c36b6 (unknown)
    @     0x7f2a241c3701 std::terminate()
    @     0x7f2a241c3969 __cxa_rethrow
    @     0x7f29dbc6ad3e caffe::PythonLayer<>::Forward_cpu()
    @     0x7f2a24b53d44 caffe::Net<>::ForwardFromTo()
    @     0x7f2a24b541c7 caffe::Net<>::ForwardPrefilled()
    @     0x7f2a24a45f43 caffe::Solver<>::Test()
    @     0x7f2a24a467c6 caffe::Solver<>::TestAll()
    @     0x7f2a24a4f85a caffe::Solver<>::Step()
    @     0x7f2a24a503d4 caffe::Solver<>::Solve()
    @           0x407e99 train()
    @           0x405be8 main
    @     0x7f2a23b76830 __libc_start_main
    @           0x406109 _start
    @                0x0 (unknown)
Aborted (core dumped)
`
***
##几句话：
`
1_  self.pool = Pool(processes=pool_size)
*Pool返回的是啥*
2_  self.join_worker()
*线程啥时候开始*

self.batch_advancer = BatchAdvancer(self.result, self.sequence_generator, self.image_processor, self.pool)*实参*
return advance_batch(self.result, self.sequence_generator, self.image_processor, self.pool)*实际起作用的函数形参*
label_r, im_info = sequence_generator()
*self.image_processor = ImageProcessorCrop(self.transformer, self.flow)*
result['data'] = pool.map(image_processor, im_info);*错误语句*
dispatch_worker(self.batch_advancer)

 #276 def dispatch_worker(self):
    assert self.thread is None
    self.thread = Thread(target=self.batch_advancer);

class videoReadTrain_RGB(videoRead):
`
