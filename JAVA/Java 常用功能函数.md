#Java 常用功能函数
```
/* * Db.java
	Created on 2007年8月20日, 上午 8:37
*/
import java.io.*;
import java.sql.*;
import java.util.Properties;

public class Db {
    private String driver;
    private String url;
    private String user;
    private String password;
    private Connection conn;
    private Statement stm;
    private ResultSet rs;

    public Db(){
        this("DBConf.properties");
    }
	
    public Db(String conf) {
        loadProperties(conf);
        setConn();
    }
	
    public Connection getConn(){
        return this.conn;
    }
	
  //handle the properties file to get the informations for connection
    private void loadProperties(String conf){
        Properties props = new Properties();
        try {
            props.load(new FileInputStream(conf));
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        this.driver = props.getProperty("driver");
        this.url = props.getProperty("url");
        this.user = props.getProperty("user");
        this.password = props.getProperty("password");
    }
    //implement the Connection
    private void setConn(){
        try {
            Class.forName(driver);
            this.conn = DriverManager.getConnection(url,user,password);
        } catch(ClassNotFoundException classnotfoundexception) {
              classnotfoundexception.printStackTrace();
            System.err.println("db: " + classnotfoundexception.getMessage());
        } catch(SQLException sqlexception) {
            System.err.println("db.getconn(): " + sqlexception.getMessage());
        }
    }
       public void doInsert(String sql) {
        try {
            Statement statement = conn.createStatement();
            int i = stm.executeUpdate(sql);
        } catch(SQLException sqlexception) {
            System.err.println("db.executeInset:" + sqlexception.getMessage());
        }
    }
    public void doDelete(String sql) {
        try {
            stm = conn.createStatement();
            int i = stm.executeUpdate(sql);
        } catch(SQLException sqlexception) {
            System.err.println("db.executeDelete:" + sqlexception.getMessage());
        }
    }
    public void doUpdate(String sql) {
        try {
            stm = conn.createStatement();
            int i = stm.executeUpdate(sql);
        } catch(SQLException sqlexception) {
            System.err.println("db.executeUpdate:" + sqlexception.getMessage());
        }
    }
    
    public ResultSet doSelect(String sql) {
        try {
            stm = conn.createStatement(java.sql.ResultSet.TYPE_SCROLL_INSENSITIVE,java.sql.ResultSet.CONCUR_READ_ONLY);
            rs = stm.executeQuery(sql);
        } catch(SQLException sqlexception) {
            System.err.println("db.executeQuery: " + sqlexception.getMessage());
        }
        return rs;
    }
    public static void main(String[] args){
        try{
            Db db = new Db();
            Connection conn = db.getConn();
            if(conn != null && !conn.isClosed()) {
                System.out.println("連結成功");
                ResultSet rs = db.doSelect("select * from content");
                while(rs.next()){
                    System.out.println(rs.getString(1)+":"+rs.getString(2)+":"+rs.getString(3));
                  }
                rs.close();
                conn.close();
            }
        }catch(SQLException e) {
            e.printStackTrace();
        }
    }  
}

```

- - -
##中文转拼音
```
/**
 * <p>
 * Title: 
 * Description:中文转换为拼音
 * @version 1.0
 */
public class ChineseSpelling {

private static int[] pyvalue = new int[] { -20319, -20317, -20304, -20295, -20292, -20283, -20265, -20257, -20242, -20230, -20051, -20036, -20032, -20026, -20002, -19990, -19986, -19982, -19976, -19805, -19784, -19775, -19774, -19763, -19756, -19751, -19746, -19741, -19739, -19728, -19725, -19715, -19540, -19531, -19525, -19515, -19500, -19484, -19479, -19467, -19289, -19288, -19281, -19275, -19270, -19263, -19261, -19249, -19243, -19242, -19238, -19235, -19227, -19224, -19218, -19212, -19038, -19023, -19018, -19006, -19003, -18996, -18977, -18961, -18952, -18783, -18774, -18773, -18763, -18756, -18741, -18735, -18731, -18722, -18710, -18697, -18696, -18526, -18518, -18501, -18490, -18478, -18463, -18448, -18447, -18446, -18239, -18237, -18231, -18220, -18211, -18201, -18184, -18183,
			-18181, -18012, -17997, -17988, -17970, -17964, -17961, -17950, -17947, -17931, -17928, -17922, -17759, -17752, -17733, -17730, -17721, -17703, -17701, -17697, -17692, -17683, -17676, -17496, -17487, -17482, -17468, -17454, -17433, -17427, -17417, -17202, -17185, -16983, -16970, -16942, -16915, -16733, -16708, -16706, -16689, -16664, -16657, -16647, -16474, -16470, -16465, -16459, -16452, -16448, -16433, -16429, -16427, -16423, -16419, -16412, -16407, -16403, -16401, -16393, -16220, -16216, -16212, -16205, -16202, -16187, -16180, -16171, -16169, -16158, -16155, -15959, -15958, -15944, -15933, -15920, -15915, -15903, -15889, -15878, -15707, -15701, -15681, -15667, -15661, -15659, -15652, -15640, -15631, -15625, -15454, -15448, -15436, -15435, -15419, -15416, -15408, -15394,
			-15385, -15377, -15375, -15369, -15363, -15362, -15183, -15180, -15165, -15158, -15153, -15150, -15149, -15144, -15143, -15141, -15140, -15139, -15128, -15121, -15119, -15117, -15110, -15109, -14941, -14937, -14933, -14930, -14929, -14928, -14926, -14922, -14921, -14914, -14908, -14902, -14894, -14889, -14882, -14873, -14871, -14857, -14678, -14674, -14670, -14668, -14663, -14654, -14645, -14630, -14594, -14429, -14407, -14399, -14384, -14379, -14368, -14355, -14353, -14345, -14170, -14159, -14151, -14149, -14145, -14140, -14137, -14135, -14125, -14123, -14122, -14112, -14109, -14099, -14097, -14094, -14092, -14090, -14087, -14083, -13917, -13914, -13910, -13907, -13906, -13905, -13896, -13894, -13878, -13870, -13859, -13847, -13831, -13658, -13611, -13601, -13406, -13404,
			-13400, -13398, -13395, -13391, -13387, -13383, -13367, -13359, -13356, -13343, -13340, -13329, -13326, -13318, -13147, -13138, -13120, -13107, -13096, -13095, -13091, -13076, -13068, -13063, -13060, -12888, -12875, -12871, -12860, -12858, -12852, -12849, -12838, -12831, -12829, -12812, -12802, -12607, -12597, -12594, -12585, -12556, -12359, -12346, -12320, -12300, -12120, -12099, -12089, -12074, -12067, -12058, -12039, -11867, -11861, -11847, -11831, -11798, -11781, -11604, -11589, -11536, -11358, -11340, -11339, -11324, -11303, -11097, -11077, -11067, -11055, -11052, -11045, -11041, -11038, -11024, -11020, -11019, -11018, -11014, -10838, -10832, -10815, -10800, -10790, -10780, -10764, -10587, -10544, -10533, -10519, -10331, -10329, -10328, -10322, -10315, -10309, -10307,
			-10296, -10281, -10274, -10270, -10262, -10260, -10256, -10254 };

private static String[] pystr = new String[] { "a", "ai", "an", "ang", "ao", "ba", "bai", "ban", "bang", "bao", "bei", "ben", "beng", "bi", "bian", "biao", "bie", "bin", "bing", "bo", "bu", "ca", "cai", "can", "cang", "cao", "ce", "ceng", "cha", "chai", "chan", "chang", "chao", "che", "chen", "cheng", "chi", "chong", "chou", "chu", "chuai", "chuan", "chuang", "chui", "chun", "chuo", "ci", "cong", "cou", "cu", "cuan", "cui", "cun", "cuo", "da", "dai", "dan", "dang", "dao", "de", "deng", "di", "dian", "diao", "die", "ding", "diu", "dong", "dou", "du", "duan", "dui", "dun", "duo", "e", "en", "er", "fa", "fan", "fang", "fei", "fen", "feng", "fo", "fou", "fu", "ga", "gai", "gan", "gang", "gao", "ge", "gei", "gen", "geng", "gong", "gou", "gu", "gua", "guai", "guan", "guang", "gui", "gun",
"guo", "ha", "hai", "han", "hang", "hao", "he", "hei", "hen", "heng", "hong", "hou", "hu", "hua", "huai", "huan", "huang", "hui", "hun", "huo", "ji", "jia", "jian", "jiang", "jiao", "jie", "jin", "jing", "jiong", "jiu", "ju", "juan", "jue", "jun", "ka", "kai", "kan", "kang", "kao", "ke", "ken", "keng", "kong", "kou", "ku", "kua", "kuai", "kuan", "kuang", "kui", "kun", "kuo", "la", "lai", "lan", "lang", "lao", "le", "lei", "leng", "li", "lia", "lian", "liang", "liao", "lie", "lin", "ling", "liu", "long", "lou", "lu", "lv", "luan", "lue", "lun", "luo", "ma", "mai", "man", "mang", "mao", "me", "mei", "men", "meng", "mi", "mian", "miao", "mie", "min", "ming", "miu", "mo", "mou", "mu", "na", "nai", "nan", "nang", "nao", "ne", "nei", "nen", "neng", "ni", "nian", "niang", "niao",
"nie", "nin", "ning", "niu", "nong", "nu", "nv", "nuan", "nue", "nuo", "o", "ou", "pa", "pai", "pan", "pang", "pao", "pei", "pen", "peng", "pi", "pian", "piao", "pie", "pin", "ping", "po", "pu", "qi", "qia", "qian", "qiang", "qiao", "qie", "qin", "qing", "qiong", "qiu", "qu", "quan", "que", "qun", "ran", "rang", "rao", "re", "ren", "reng", "ri", "rong", "rou", "ru", "ruan", "rui", "run", "ruo", "sa", "sai", "san", "sang", "sao", "se", "sen", "seng", "sha", "shai", "shan", "shang", "shao", "she", "shen", "sheng", "shi", "shou", "shu", "shua", "shuai", "shuan", "shuang", "shui", "shun", "shuo", "si", "song", "sou", "su", "suan", "sui", "sun", "suo", "ta", "tai", "tan", "tang", "tao", "te", "teng", "ti", "tian", "tiao", "tie", "ting", "tong", "tou", "tu", "tuan", "tui", "tun",
"tuo", "wa", "wai", "wan", "wang", "wei", "wen", "weng", "wo", "wu", "xi", "xia", "xian", "xiang", "xiao", "xie", "xin", "xing", "xiong", "xiu", "xu", "xuan", "xue", "xun", "ya", "yan", "yang", "yao", "ye", "yi", "yin", "ying", "yo", "yong", "you", "yu", "yuan", "yue", "yun", "za", "zai", "zan", "zang", "zao", "ze", "zei", "zen", "zeng", "zha", "zhai", "zhan", "zhang", "zhao", "zhe", "zhen", "zheng", "zhi", "zhong", "zhou", "zhu", "zhua", "zhuai", "zhuan", "zhuang", "zhui", "zhun", "zhuo", "zi", "zong", "zou", "zu", "zuan", "zui", "zun", "zuo" };

private StringBuilder buffer;

private String resource;

private static ChineseSpelling chineseSpelling = new ChineseSpelling();

public static ChineseSpelling getInstance() {
return chineseSpelling;
}

public String getResource() {
return resource;
}

public void setResource(String resource) {
		this.resource = resource;
}

private int getChsAscii(String chs) {
int asc = 0;
try {

byte[] bytes = chs.getBytes("gb2312");

if (bytes == null || bytes.length > 2 || bytes.length <= 0) { // 错误

// log

throw new RuntimeException("illegal resource string");
// System.out.println("error");

}
if (bytes.length == 1) { // 英文字符

				asc = bytes[0];
}
if (bytes.length == 2) { // 中文字符

int hightByte = 256 + bytes[0];
int lowByte = 256 + bytes[1];
				asc = (256 * hightByte + lowByte) - 256 * 256;
}
} catch (Exception e) {
			System.out.println("ERROR:ChineseSpelling.class-getChsAscii(String chs)" + e);
// e.printStackTrace();

}
return asc;
}

public String convert(String str) {
		String result = null;
int ascii = getChsAscii(str);
// System.out.println(ascii);

if (ascii > 0 && ascii < 160) {
			result = String.valueOf((char) ascii);
} else {
for (int i = (pyvalue.length - 1); i >= 0; i--) {
if (pyvalue[i] <= ascii) {
					result = pystr[i];
break;
}
}
}
return result;
}

public String getSelling(String chs) {
		String key, value;
		buffer = new StringBuilder();
for (int i = 0; i < chs.length(); i++) {
			key = chs.substring(i, i + 1);
if (key.getBytes().length == 2) {
				value = (String) convert(key);
if (value == null) {
					value = "unknown";
}
} else {
				value = key;
}

			buffer.append(value);
}
return buffer.toString();
}

public String getSpelling() {
return this.getSelling(this.getResource());
}

public static void main(String[] args) {
// ChineseSpelling finder = new ChineseSpelling();


		ChineseSpelling finder = ChineseSpelling.getInstance();
		finder.setResource("中文字符");
		System.out.println(finder.getSpelling());
		System.out.println(finder.getSelling("英文字符Eng"));
}

}


```

---
##文件代码拷贝
```
import java.io.*; 
import java.util.ArrayList; 
import java.util.List; 
public class FileCopy { 
private String message = ""; 
public String getMessage() { 
  return message; 
} 
public void setMessage(String message) { 
  this.message = message; 
} 
/** 
  * 将源文件拷贝到目标文件 
  * 
  * @param src 
  *            写源文件地址，需文件名 
  * @param des 
  *            写目标文件地址，无需文件名 
  */ 
public boolean copyFile(String src, String des) { 
  File srcFile = new File(src); 
  File desDir = new File(des); 
  File desFile = new File(des + "/" + srcFile.getName()); 
  // 判断源文件是否存在 
  if (!srcFile.exists()) { 
   this.setMessage("源文件不存在！"); 
   return false; 
  } else if (!srcFile.isFile()) { 
   this.setMessage("源文件格式错！"); 
   return false; 
  } 
  // 判断源文件是否存在 
  if (!desDir.exists()) { 
   this.setMessage("目标目录不存在！"); 
   return false; 
  } else if (!desDir.isDirectory()) { 
   this.setMessage("不是有效的目录！"); 
   return false; 
  } 
  BufferedReader reader = null; 
  BufferedWriter writer = null; 
  String str; 
  try { 
   reader = new BufferedReader(new FileReader(srcFile)); 
   writer = new BufferedWriter(new FileWriter(desFile)); 
   // 判断目标文件是否存在及其格式，不存在就创建，格式不对先删除，存在就替代 
   if (!desFile.exists() || !desFile.isFile()) { 
    if (desFile.exists()) { 
     desFile.delete(); 
    } 
    desFile.createNewFile(); 
   } 
   // 从源文件读取数据，并写入目标文件 
   str = reader.readLine(); 
   while (str != null) { 
    writer.write(str); 
    writer.newLine(); 
    str = reader.readLine(); 
   } 
  } catch (IOException e) { 
   this.setMessage(e.getMessage()); 
   return false; 
  } finally { 
   if (reader != null) { 
    try { 
     reader.close(); 
    } catch (IOException e) { 
     this.setMessage(e.getMessage()); 
    } 
   } 
   if (writer != null) { 
    try { 
     writer.close(); 
    } catch (IOException e) { 
     this.setMessage(e.getMessage()); 
    } 
   } 
  } 
  return true; 
} 
private List fileList = new ArrayList(); 

/** 
  * 列出所有文件 
  * @param srcFile 
  */ 
private void file(File srcFile) { 
  if (srcFile.isDirectory()) { 
   String[] files = srcFile.list(); 
   
   for (int i = 0; i < files.length; i++) { 
    File f = new File(srcFile + "/" + files[i]); 
    // 如果是文件加入列表，否则递归列出 
    if (f.isFile()) { 
     fileList.add(f); 
    } else 
     file(f); 
   } 
  }else this.setMessage(srcFile.getAbsolutePath()+"不是目录"); 
} 
/** 
  * 建立目录 
  * @param des 
  * @throws IOException 
  */private void mkdir(File des) { 
  if (!des.exists() || !des.isDirectory()) { 
   mkdir(des.getParentFile()); 
   if (des.exists()) { 
    des.delete(); 
   } 
   des.mkdir(); 
  } 
} 
/** 
  * 复制目录  将源目录下所有文件拷贝到目标目录下 
  * @param src  源目录 
   * @param des  目标目录 
  */ 
public boolean copyDir(String src, String des) { 
  File srcFile = new File(src); 
  if (!srcFile.exists()) { 
   this.setMessage("源目录不存在！"); 
   return false; 
  } else if (!srcFile.isDirectory()) { 
   this.setMessage(src+"不是有效的目录！"); 
   return false; 
  } 
  file(srcFile); 
   
  for (int i = 0; i < fileList.size(); i++) { 
   String srcName = ((File) fileList.get(i)).getPath(); 
   String desName = srcName.substring(src.length(), srcName.length()); 
   desName = des + desName; 
   File dir=new File(desName).getParentFile(); 
   mkdir(dir); 
   
   if(!copyFile(srcName, dir.getPath())){ 
    return false; 
   } 
  } 
  return true; 
} 
public static void main(String[] args) { 

  FileCopy t = new FileCopy(); 
  System.out.println(t.copyFile("D:/aaa.txt","E:")); 
  String src="D:/asdf"; 
  String des="E:/adf"; 
  System.out.println(t.copyDir(src, des)); 
  System.out.println(t.getMessage()); 
} 

}


```







