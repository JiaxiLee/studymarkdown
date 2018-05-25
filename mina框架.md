##Apache Mina 框架使用 ##

### 一、简介###

Apache的MINA框架（Ioservice、Iofilter、IoHandler）是tcp/ip的一个通讯框架；

IoService -- > new NioSocketAcceptor()

Iofilter --- > MessageDecoder MessageEncoder

IoHandler -- > IoHandlerAdapter

###二、IoService使用###

		this.acceptor = new NioSocketAcceptor();
		//定义接收消息编码
		this.acceptor.getFilterChain().addLast(
				"codec", 
				new ProtocolCodecFilter(new DspmDataCodecFactory(
						new DspmMessageDecoder(),
						new DspmMessageEncoder())));
		this.addLog("开始启动数据监听服务");
		
		// 启用线程池
		java.util.concurrent.Executor threadPool = 
				java.util.concurrent.Executors.newCachedThreadPool();
		this.acceptor.getFilterChain().addLast("threadPool", new ExecutorFilter(threadPool));
		this.addLog("设置线程池成功");
		
		// 设置读缓冲区大小
		acceptor.getSessionConfig().setReadBufferSize(2048);
		this.addLog("设置读缓存区为 2048字节");
		
		// 自定义参数mina连接空闲时间则进入空闲
		int freeTime = SysParamBuffer.Instance.getMinaTimeConn();
		acceptor.getSessionConfig().setIdleTime(IdleStatus.BOTH_IDLE, 5*60);
		this.addLog("设置空闲等待时间为"+freeTime+"秒");
		
		// 非延时发送
		acceptor.getSessionConfig().setTcpNoDelay(true);
		this.addLog("设置非延时发送");
		
		// 设置服务器并发连接数
		acceptor.setBacklog(20000);
		this.addLog("设置后备队列为20000");
		
		// 设置事件逻辑处理器
		acceptor.setHandler(new DspmDataHandler());
		this.addLog("设置逻辑处理器");
		
		try{
			// 绑定端口
			acceptor.bind(new InetSocketAddress(PORT));
			this.status = EServiceStatus.Running;
			logger.info("服务器启动成功########################+端口号为："+PORT);
			this.addLog("监听服务启动成功");
			return true;
			
		}catch(java.io.IOException ex){
			logger.error(ex.getMessage(),ex);
			this.addLog("启动监控服务失败：" + ex.getMessage());
			return false;
		}


###三、IoFilter 的使用###

	public class DspmDataCodecFactory extends DemuxingProtocolCodecFactory {
	
		public DspmDataCodecFactory(
				MessageDecoder decoder, 
				MessageEncoder<EaimData> encoder){
			this.addMessageDecoder(decoder);
			this.addMessageEncoder(EaimData.class, encoder);
		}
	}

	public class DspmMessageDecoder implements MessageDecoder {
	private static final Logger logger = Logger.getLogger(DspmMessageDecoder.class); 
	@Override
	public MessageDecoderResult decodable(IoSession session, IoBuffer in) {//主要是对解码的工作准备，
		//字节数必须满足到的个数
		if(in.remaining() < 2)//此缓冲区中的剩余元素数
			return MessageDecoderResult.NEED_DATA;
		
		byte[] bDataType = new byte[2];
		
		byte[] bTotalLength = new byte[2];
		in.get(bDataType);//此方法将此缓冲区的字节传输到给定的目标数组中。
		
		//System.out.println("接收到的字节为。。。。。。。。。。。。");
		EaimUtil.print(bDataType);
		int typeCode = 0;
		typeCode += ((bDataType[0] & 0xFF) << 8 );
		typeCode += ((bDataType[1] & 0xFF) << 0);
		/*if(typeCode == DataDeviceSimpleHeartBeatReq.DATA_TYPE 
				|| typeCode == DataDeviceSimpleHeartBeatResp.DATA_TYPE){
			session.setAttribute("Size", 2);
			return MessageDecoderResult.OK;
		}*/
		
		if(UnframeData.isUnframeData(typeCode)){
			//int length = UnframeData.;
			UnframeData data = UnframeData.createInstance(typeCode);
			session.setAttribute("Size", data.getLength());
			return MessageDecoderResult.OK;
		}
		
		in.get(bTotalLength);
		int totalLength = 0;
		
		totalLength += ((bTotalLength[0] & 0xFF) << 8);
		totalLength += ((bTotalLength[1] & 0xFF) << 0);
		if(in.remaining() < totalLength - 4)
			return MessageDecoderResult.NEED_DATA;
		
		session.setAttribute("Size", totalLength);
		return MessageDecoderResult.OK;
	}
	
	@Override
	public MessageDecoderResult decode(IoSession session, IoBuffer in,
			ProtocolDecoderOutput out)  {
		int size = (Integer)session.getAttribute("Size");
		byte[] buffer = new byte[size];
		in.get(buffer);
		int dataType = 0;//报文类型
		dataType += ((buffer[0] & 0xFF) << 8);
		dataType += ((buffer[1] & 0xFF) << 0);
		
	
		if(UnframeData.isUnframeData(dataType)){
			UnframeData data = UnframeData.createInstance(dataType);
			data.parseData(buffer);//解析出来字节数组
			out.write(data);
			return MessageDecoderResult.OK;
		}
		try {
			//获取采集器的序列号，这样才知道对应采集器的解码秘钥
			int encodeType = 1;//编码方式
			String typeName = EEncodingType.valueOf(encodeType).getEnumName();//编码名称
			int snSize = ((buffer[9] & 0xFF) << 0);//序列号长度
			byte[] snByte = new byte[snSize];
			System.arraycopy(buffer, 10, snByte, 0, snSize);//采集器序号的byte数组
			String collectorSn = new String(snByte, typeName);
			CollectorData collectorData = CollectorBuffer.Instance.getBySn(collectorSn);
			byte[] key = null;
			if (null == collectorData) {
				logger.error("采集器序列号不存在"+collectorSn);
				throw new RuntimeException("Colletorsn is Error");
			}
			byte[] base_key = EaimUtil.fromBCDString(collectorData.getCollector().getBaseKey());
			CollectorModelBuffer.Instance.getById(collectorData.getCollector().getCollectorModelId()).getMacAlgorithmEnum(); 
			EMacAlgorith algorith = CollectorModelBuffer.Instance.
					getById(collectorData.getCollector().getCollectorModelId())
					.getMacAlgorithmEnum(); 
			switch (algorith) {
			case BaseDES:
				if (dataType == DataLoginReq.DATA_TYPE) {
					key = null;
				}else if (dataType ==  DataLoginCheckReq.DATA_TYPE) {
					key = base_key;
				}else {
					key = IDBKDesUtil.encrypt(EaimUtil.fromBCDString(collectorData.getCollector().getWorkKey()
							),base_key);
				}
				break;
			case MD5Only:
				key = null;
				break;
			case None:
				key = null;
				break;
			}
			session.setAttribute("collectorSn", collectorSn);
			Integer collectorId = collectorData.getCollector().getId();
			session.setAttribute("CollectorId", collectorId);
			if (collectorData.getCollector().getRemoved() && 
					collectorData.getCollector().getStationUnitBound()==null) {
				logger.error("采集器已移除或未绑定");
				EaimData data = EaimData.createInstance(0);
				data.setDataParseResult(EDataParseResult.UnrecognizeData);
				out.write(data);
			}else{
				EaimData data = EaimData.createInstance(dataType);
				data.parseData(buffer,new MacCalculator(),key,algorith);
				logger.error("正常请求的字节的字节为："+EaimUtil.toBCDString(buffer));
				out.write(data);
			}
		} catch (Exception e) {
			e.printStackTrace();
			logger.error(e.getMessage(),e);
			EaimData data = EaimData.createInstance(0);
			logger.error(print(buffer));
			data.setDataParseResult(EDataParseResult.UnrecognizeData);
			out.write(data);
		}
		return MessageDecoderResult.OK;
	}
	
	/**
	 * 
	 * @Description  把字节输出为字符串
	 * @author lijiaxi
	 * @date 2016-12-14
	 */
		public static String print(byte[] data){
			String pre = "0x";
			StringBuilder sb = new StringBuilder();
			for(byte b : data){
				String text = Integer.toHexString(0xFF & b);
				if(text.length() == 1)
					text = "0" + text;
				sb.append(pre);
				sb.append(text);
				pre = ", 0x";
			}
			return sb.toString();
		}
		
		@Override
		public void finishDecode(IoSession session, ProtocolDecoderOutput out)
				throws Exception {
			
		}
	}


		
	public class DspmMessageEncoder implements MessageEncoder<EaimData> {
		
		private static final Logger logger = Logger.getLogger(DspmMessageEncoder.class);
		
		@Override
		public void encode(IoSession session, EaimData object, ProtocolEncoderOutput out)
				throws Exception {
			
				IllegalityReq message = (IllegalityReq) object;
			
				bData = message.toByteArray(DataDeviceSimpleHeartBeatResp.DATA_TYPE);
			
				IoBuffer buffer = IoBuffer.allocate(bData.length).setAutoExpand(true);
				buffer.put(bData).flip();
				out.write(buffer);
			
		
		}
	}