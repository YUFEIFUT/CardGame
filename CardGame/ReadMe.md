# 软件工程第三次作业----第五题

## 1.数据流图

![df](C:\Users\34655\Desktop\df.PNG)

## 2.软件质量测试

### 2.1采取了哪些质量保证手段

​	采用自动分析工具SonarQube对代码进行了分析

### 2.2程序代码风格

#### 2.2.1常量类

```java
package com.yufei.game;


public class Constant {
	private Constant() {
	}
	public static final int GAME_WIDTH = 1024;
	public static final  int  GAME_HEIGHT = 700;
	
	//三张牌的类型
	public static final int STRAIGHT_FLUSH = 4;//同花顺
	public static final int SAME_POINTS = 3;//同点
	public static final int PAIR = 2;//对子
	public static final int OTHERS = 1;//其他
	
	
	//扑克牌面值
	public static final int TWO = 2;
	public static final int THREE = 3;
	public static final int FOUR = 4;
	public static final int FIVE = 5;
	public static final int SIX = 6;
	public static final int SEVEN = 7;
	public static final int EIGHT = 8;
	public static final int NINE = 9;
	public static final int TEN = 10;
	public static final int J = 11;
	public static final int Q = 12;
	public static final int K = 13;
	public static final int A =14;
	
	//扑克牌花色
	public static final int DIAMONDS = 4;
	public static final int CLUBS = 3;//梅花
	public static final int HERSRTS = 2;
	public static final int SPADES = 1;//黑桃
}

```

#### 2.2.2主要类MyGame

```java
package com.yufei.game;

import java.awt.Color;
import java.awt.Font;
import java.awt.Frame;
import java.awt.Graphics;
import java.awt.Image;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
import java.util.ArrayList;
import java.util.List;




public class MyGame extends Frame {
	/**
	 * 
	 */
	private static final long serialVersionUID = 5427877546954545228L;
	transient Image bg = GameUtil.getImage("images/bg3.jpg");
	
	transient Image icon = GameUtil.getImage("Pokers/rear.gif");
	
	transient Image p1t = GameUtil.getImage("players/p1.jpg");
	transient Image p2t = GameUtil.getImage("players/p2.jpg");
	
	//男
	transient Player p1;
	//女
	transient Player p2;
	
	transient List<Poker> pokers = new ArrayList<Poker>();
	
	class   KeyMonitor extends  KeyAdapter  {
		@Override
		public void keyPressed(KeyEvent e) {
			if(e.getKeyCode() == KeyEvent.VK_ENTER) {
				setVisible(false);
				MyGame  f = new MyGame();
				f.launchFrame();
			}
		}
	}
	
	@Override
	public void paint(Graphics g) {
		g.drawImage(bg, 0, 0, null);
		
		g.drawImage(p1t, 500, 60, null);
		g.drawImage(p2t, 30, 500, null);
		
		for(int i=0;i<3;i++) {
			g.drawImage(p1.pokers.get(i).img, (int)p1.pokers.get(i).x, (int)p1.pokers.get(i).y, null);
		}
		
		for(int i=0;i<3;i++) {
			g.drawImage(p2.pokers.get(i).img, (int)p2.pokers.get(i).x, (int)p2.pokers.get(i).y, null);
		}
		
		Color   c =  g.getColor();
		g.setColor(Color.red);
		Font   f  =  new Font("华文楷体", Font.BOLD, 35);
		
		g.drawString("TYPE ENTER TO DO ONE MORE TIME", 800, 100);
		
		g.setFont(f);
		if(p1.cardsType > p2.cardsType) {
			if(p1.cardsType == Constant.STRAIGHT_FLUSH) {
				g.drawString("The Winner is The Cool Boy With STRAIGHT_FLUSH", 150, 300);
			}else if(p1.cardsType == Constant.SAME_POINTS) {
				g.drawString("The Winner is The Cool Boy With SAME_POINTS", 150, 300);
			}else {
				g.drawString("The Winner is The Cool Boy With Pair", 150, 300);
			}
		}else if(p1.cardsType < p2.cardsType) {
			if(p1.cardsType == Constant.STRAIGHT_FLUSH) {
				g.drawString("The Winner is The Beauty With STRAIGHT_FLUSH", 150, 300);
			}else if(p1.cardsType == Constant.SAME_POINTS) {
				g.drawString("The Winner is The Beauty With SAME_POINTS", 150, 300);
			}else {
				g.drawString("The Winner is The Beauty With Pair", 150, 300);
			}
		}else {
			//同类型的牌
			//比大小
			if(p1.nums > p2.nums) {
				g.drawString("The Winner is The Cool Boy With Ragged", 150, 300);
			}else if(p1.nums < p2.nums) {
				g.drawString("The Winner is The Beauty With Ragged", 150, 300);
			}else {
				g.drawString("A DEAD HEAT", 150, 300);
			}
		}
		
		g.setColor(c);
	}
	
	//帮助我们反复的重画窗口！
	class  PaintThread  extends  Thread  {
		@Override
		public void run() {
			while(true){
				repaint();		//重画
				
				try {
					Thread.sleep(40);   	//1s=1000ms
				} catch (Exception e) {
					e.printStackTrace();
				}		
			}
		}
		
	}
	
	public static void main(String[] args) {
		MyGame  f = new MyGame();
		f.launchFrame();
	}
	
	public void launchFrame() {
		this.setIconImage(icon);
		this.setTitle("卡牌游戏");
		this.setVisible(true);
		this.setSize(Constant.GAME_WIDTH, Constant.GAME_HEIGHT);
		this.setLocationRelativeTo(null);
		
		this.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});
		
		new PaintThread().start();
		addKeyListener(new KeyMonitor());
		
		//将所有的牌放到这个容器里面
		for(int i=1;i<=4;i++) {
			for(int j=2;j<=14;j++) {
				Image img = GameUtil.getImage("Pokers/"+i+"-"+j+".gif");
				Poker poker = new Poker(img, j, i);
				
				pokers.add(poker);
			}
		}
		
		//创建玩家和玩家的牌
		//玩家的牌
		List<Poker> pokers1 = new ArrayList<Poker>();
		List<Poker> pokers2 = new ArrayList<Poker>();
		//给玩家一发牌
		for(int i=0;i<3;i++) {
			//从已有的牌中随便选一个，得到它的索引
			int index = (int)(pokers.size()*Math.random());
			//然后把它发给某个玩家
			pokers1.add(pokers.get(index));
			
			//给牌加上位置
			pokers1.get(i).x = 30;
			pokers1.get(i).y = 120+i*100;
			
			//牌里面就会少一张牌
			pokers.remove(index);
		}
		p1 = new Player(pokers1);
		
		//给玩家二发牌
		for(int i=0;i<3;i++) {
			//从已有的牌中随便选一个，得到它的索引
			int index = (int)(pokers.size()*Math.random());
			//然后把它发给某个玩家
			pokers2.add(pokers.get(index));
			
			//给牌加上位置
			pokers2.get(i).x = 430+i*100;
			pokers2.get(i).y = 300;
			
			//牌里面就会少一张牌
			pokers.remove(index);
		}
		p2 = new Player(pokers2);
	}
	
	private transient Image offScreenImage = null;
	@Override
	public void update(Graphics g) {
	    if(offScreenImage == null)
	        offScreenImage = this.createImage(Constant.GAME_WIDTH,Constant.GAME_HEIGHT);//这是游戏窗口的宽度和高度
	     
	    Graphics gOff = offScreenImage.getGraphics();
	    paint(gOff);
	    g.drawImage(offScreenImage, 0, 0, null);
	}
}

```

#### 2.2.4程序结构

![1570539703485](C:\Users\34655\AppData\Roaming\Typora\typora-user-images\1570539703485.png)

### 2.3程序代码质量

![1570539744234](C:\Users\34655\AppData\Roaming\Typora\typora-user-images\1570539744234.png)



## 3.软件测试

### 3.1采用的测试工具Junit

### 3.2测试类

```java
package com.yufei.test;

import org.junit.Test;
import static org.junit.Assert.assertEquals;
public class TestJunit {
   @Test
   public void testAdd() {
      String str= "Junit is working fine";
      assertEquals("Junit is working fine",str);
   }
}
```

```

```



```java
package com.yufei.test;
import org.junit.runner.JUnitCore;

import org.junit.runner.Result;
import org.junit.runner.notification.Failure;

public class TestRunner {
   public static void main(String[] args) {
      Result result = JUnitCore.runClasses(TestJunit.class);
      for (Failure failure : result.getFailures()) {
         System.out.println(failure.toString());
      }
      System.out.println(result.wasSuccessful());
   }
}   
```

### 3.3测试演示

![1570539989412](C:\Users\34655\AppData\Roaming\Typora\typora-user-images\1570539989412.png)

