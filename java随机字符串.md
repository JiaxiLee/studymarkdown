##java随机字符串生成##

代码如下：

	/**
	 * 随机序列号生成器
	 * @author 肖志民
	 *
	 */
	public class RandomSnBuilder {
	
		private static final Random rdm = new Random();
		private static final char[] Source = new char[]{
			'N', '0', 'P', 'R', 'U', 
			'2', 'Z', 'B', '7', 'D', 
			'S', 'T', '3', 'V', 'W', 
			'C', 'A', 'E', '5', 'M', 
			'G', 
			'H', 'L', 'I', 'J', 'K', 
			'1', 'O','6', 'X', '4', 
			'Q', '9', 'Y', '8', 'F'
		};
		
		public static boolean checkSnFormat(String sn){
			int sum = 0;
			for(int i=0; i<sn.length() - 1; i++)
				sum += sn.charAt(i);
			
			char check = Source[sum % Source.length];
			if(check == sn.charAt(sn.length() - 1))
				return true;
			else
				return false;
		}
		
		public static String nextRandomText(int length){
			char[] buff = new char[length];
			int sum = 0;
			for(int i=0; i<buff.length-1; i++){
				char c = Source[rdm.nextInt(Source.length)];
				sum += c;
				buff[i] = c;
			}
			
			buff[buff.length - 1] = Source[sum % Source.length];
			
			return new String(buff);
		}
		
		private RandomSnBuilder(){
		}
	}
