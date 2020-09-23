<div align="center">

## Funky animated Java Banner


</div>

### Description

To spice up your web page. Comments are in French

Written by:http://www.nctech.fr/Zimmermann/public-html/Applets/Animation/AnimCercles.html
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Found on the Web](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/found-on-the-web.md)
**Level**          |Unknown
**User Rating**    |5.9 (606 globes from 102 users)
**Compatibility**  |Java \(JDK 1\.1\)
**Category**       |[Applet](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/applet__2-81.md)
**World**          |[Java](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/java.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/found-on-the-web-funky-animated-java-banner__2-268/archive/master.zip)

### API Declarations

```
<APPLET CODE="AnimCercles.class"
WIDTH="350" HEIGHT="180" ALIGN="BOTTOM">
<PARAM NAME="bgColor" VALUE="blue">
<PARAM NAME="fgColor" VALUE="green">
<PARAM NAME="bgType" VALUE="Oval">
<PARAM NAME="text" VALUE="Java !!!">
<PARAM NAME="url" VALUE="http://www.nctech.fr/">
<PARAM NAME="font" VALUE="TimesRoman">
<PARAM NAME="style" VALUE="BOLDITALIC">
<PARAM NAME="textColor" VALUE="red">
<PARAM NAME="size" VALUE="95">
<PARAM NAME="playBack" VALUE="0">
<PARAM NAME="step" VALUE="12">
<PARAM NAME="timeSleep" VALUE="30">
</APPLET>
```


### Source Code

```
import java.awt.*;
import java.util.StringTokenizer;
/** Affiche un message sur un fond de cercles concentriques animes.
 *	Vos remarques a vincent.zimmermann@nctech.fr
 *	Utilisable librement.
 *
 *	Exemple d'utilisation dans une page HTML :
 *	<APPLET CODE="AnimCercles.class" WIDTH=350 HEIGHT=200>
 *	<PARAM NAME="bgColor" VALUE="blue">
 *	<PARAM NAME="fgColor" VALUE="green">
 *	<PARAM NAME="bgType" VALUE="RoundRect">
 *	<PARAM NAME="cornerX" VALUE="35">
 *	<PARAM NAME="cornerY" VALUE="25">
 *	<PARAM NAME="text" VALUE="Java !!!">
 *	<PARAM NAME="font" VALUE="TimesRoman">
 *	<PARAM NAME="style" VALUE="BOLD">
 *	<PARAM NAME="textColor" VALUE="red">
 *	<PARAM NAME="size" VALUE="90">
 *	<PARAM NAME="playBack" VALUE="0">
 *	<PARAM NAME="step" VALUE="10">
 *	<PARAM NAME="timeSleep" VALUE="15">
 *	Il aurait du y avoir une applet ici si votre Browser avait supporte Java...
 *	</APPLET>
 *
 *	Explications :
 *	 font : Helvetica, TimesRoman, Courier, Dialog
 *	 style : PLAIN, BOLD, ITALIC, BOLDITALIC
 *	 bgColor & fgColor : white, black, lightGray, gray, darkGray, red, green,
 *  & textColor     blue, yellow, magenta, cyan, pink, orange
 *	     (RGB)  : 255:255:255
 *	     (RGB)  : 255,255,255
 *	     (RGB)  : 255.255.255
 *	playBack : 0 ou 1
 *	bgType : Oval, Rect, RoundRect
 *	et pour le RoundRect, specifier le format du coin arrondis (cornerX, cornerY)
 *
 */
public class AnimCercles extends java.applet.Applet implements Runnable
{
	// On cree un Thread pour l'applet
	Thread runner;
	// Le message
	String text;
	// La police du message
	Font f;
	String fontString;
	int style;
	int size;
	Color textColor;
	// Position du message
	int x;
	int y;
	// Taille de l'ecran
	int width;
	int height;
	// Le max entre width et height.
	int max;
	// Pour le double buffering (eviter les scintillements)
	Image offscreenImg;
	Graphics offscreenG;
	// Les fonds de l'ecran (pour gagner en rapidite dans l'anim)
	Image[] fondEcran = new Image[6];
	Graphics[] fondEcranG = new Graphics[6];
	// Couleur du fond
	Color bgColor;
	Color fgColor;
	// Le type de motif du fond (Oval, Rect, RoundRect)
	String bgType;
	// Dans le cas d'un RoundRect, la taille de l'arrondis
	int cornerX;
	int cornerY;
	// Decalage entre 2 cerles
	int step;
	// Temps d'attente entre chaque plans de l'animation
	int timeSleep;
	// L'animation fait un aller retour si playBack = 1
	int playBack;
	// Pour la gestion des couleurs RGB
  String rgbDelimiter = ":,.";
  StringTokenizer st;
	public void init()
	{
		// Recuperation des parametres de l'Applet
		String s;
		Integer intObj;
		// Pour obtenir la taille de l'Applet en pixels.
    try
    {
      intObj = new Integer(getParameter("width"));
      width = intObj.intValue();
    }
    catch (Exception e)
    {
      width = 300;
    }
    try
    {
      intObj = new Integer(getParameter("height"));
      height = intObj.intValue();
    }
    catch (Exception e)
    {
      height = 100;
    }
  	s = getParameter("bgType");
    if (s == null)
      bgType = "Oval";
    else if (s.equalsIgnoreCase("Rect"))
      bgType = "Rect";
    else if (s.equalsIgnoreCase("RoundRect"))
      bgType = "RoundRect";
    else
      bgType = "Oval";
    try
    {
      intObj = new Integer(getParameter("cornerX"));
      cornerX = intObj.intValue();
    }
    catch (Exception e)
    {
      cornerX = 25;
    }
    try
    {
      intObj = new Integer(getParameter("cornerY"));
      cornerY = intObj.intValue();
    }
    catch (Exception e)
    {
      cornerY = 25;
    }
		text = getParameter("text");
		if (text == null)
			text = "NCTech !";
  	s = getParameter("font");
    if (s == null)
      fontString = "TimesRoman";
    else if (s.equalsIgnoreCase("TimesRoman"))
      fontString = "TimesRoman";
    else if (s.equalsIgnoreCase("Courier"))
      fontString = "Courier";
    else if (s.equalsIgnoreCase("Helvetica"))
      fontString = "Helvetica";
    else if (s.equalsIgnoreCase("Dialog"))
      fontString = "Dialog";
    else
      fontString = "TimesRoman";
    s = getParameter("style");
    if (s == null)
      style = Font.PLAIN;
    else if (s.equalsIgnoreCase("PLAIN"))
      style = Font.PLAIN;
    else if (s.equalsIgnoreCase("BOLD"))
      style = Font.BOLD;
    else if (s.equalsIgnoreCase("ITALIC"))
      style = Font.ITALIC;
    else if (s.equalsIgnoreCase("BOLDITALIC") || s.equalsIgnoreCase("ITALICBOLD"))
      style = Font.BOLD + Font.ITALIC;
    else
      style = Font.PLAIN;
    try
    {
      intObj = new Integer(getParameter("size"));
      size = intObj.intValue();
    }
    catch (Exception e)
    {
      size = 20;
    }
    s = getParameter("textColor");
    if (s != null) st = new StringTokenizer(s, rgbDelimiter);
    if (s == null)
      textColor = Color.lightGray;
    else if (s.equalsIgnoreCase("white"))
      textColor = Color.white;
    else if (s.equalsIgnoreCase("black"))
      textColor = Color.black;
    else if (s.equalsIgnoreCase("lightGray"))
      textColor = Color.lightGray;
    else if (s.equalsIgnoreCase("gray"))
      textColor = Color.gray;
    else if (s.equalsIgnoreCase("darkGray"))
      textColor = Color.darkGray;
    else if (s.equalsIgnoreCase("red"))
      textColor = Color.red;
    else if (s.equalsIgnoreCase("green"))
      textColor = Color.green;
    else if (s.equalsIgnoreCase("blue"))
      textColor = Color.blue;
    else if (s.equalsIgnoreCase("magenta"))
      textColor = Color.magenta;
    else if (s.equalsIgnoreCase("cyan"))
      textColor = Color.cyan;
    else if (s.equalsIgnoreCase("pink"))
      textColor = Color.pink;
    else if (s.equalsIgnoreCase("orange"))
      textColor = Color.orange;
    else if (st.countTokens() == 3) {
      Integer r = new Integer(st.nextToken());
      Integer g = new Integer(st.nextToken());
      Integer b = new Integer(st.nextToken());
      textColor = new Color(r.intValue(), g.intValue(), b.intValue());
    } else
      textColor = Color.yellow;
    try
    {
      intObj = new Integer(getParameter("step"));
      step = intObj.intValue();
    }
    catch (Exception e)
    {
      step = 5;
    }
    try
    {
      intObj = new Integer(getParameter("timeSleep"));
      timeSleep = intObj.intValue();
    }
    catch (Exception e)
    {
      timeSleep = 10;
    }
    try
    {
      intObj = new Integer(getParameter("playBack"));
      playBack = intObj.intValue();
    }
    catch (Exception e)
    {
      playBack = 1;
    }
    s = getParameter("bgColor");
    if (s != null) st = new StringTokenizer(s, rgbDelimiter);
    if (s == null)
      bgColor = Color.lightGray;
    else if (s.equalsIgnoreCase("white"))
      bgColor = Color.white;
    else if (s.equalsIgnoreCase("black"))
      bgColor = Color.black;
    else if (s.equalsIgnoreCase("gray"))
      bgColor = Color.gray;
    else if (s.equalsIgnoreCase("darkGray"))
      bgColor = Color.darkGray;
    else if (s.equalsIgnoreCase("red"))
      bgColor = Color.red;
    else if (s.equalsIgnoreCase("green"))
      bgColor = Color.green;
    else if (s.equalsIgnoreCase("blue"))
      bgColor = Color.blue;
    else if (s.equalsIgnoreCase("yellow"))
      bgColor = Color.yellow;
    else if (s.equalsIgnoreCase("magenta"))
      bgColor = Color.magenta;
    else if (s.equalsIgnoreCase("cyan"))
      bgColor = Color.cyan;
    else if (s.equalsIgnoreCase("pink"))
      bgColor = Color.pink;
    else if (s.equalsIgnoreCase("orange"))
      bgColor = Color.orange;
    else if (st.countTokens() == 3) {
      Integer r = new Integer(st.nextToken());
      Integer g = new Integer(st.nextToken());
      Integer b = new Integer(st.nextToken());
      bgColor = new Color(r.intValue(), g.intValue(), b.intValue());
    } else
      bgColor = Color.lightGray;
    s = getParameter("fgColor");
    if (s != null) st = new StringTokenizer(s, rgbDelimiter);
    if (s == null)
      fgColor = Color.lightGray;
    else if (s.equalsIgnoreCase("white"))
      fgColor = Color.white;
    else if (s.equalsIgnoreCase("black"))
      fgColor = Color.black;
    else if (s.equalsIgnoreCase("lightGray"))
      fgColor = Color.lightGray;
    else if (s.equalsIgnoreCase("gray"))
      fgColor = Color.gray;
    else if (s.equalsIgnoreCase("darkGray"))
      fgColor = Color.darkGray;
    else if (s.equalsIgnoreCase("red"))
      fgColor = Color.red;
    else if (s.equalsIgnoreCase("green"))
      fgColor = Color.green;
    else if (s.equalsIgnoreCase("blue"))
      fgColor = Color.blue;
    else if (s.equalsIgnoreCase("magenta"))
      fgColor = Color.magenta;
    else if (s.equalsIgnoreCase("cyan"))
      fgColor = Color.cyan;
    else if (s.equalsIgnoreCase("pink"))
      fgColor = Color.pink;
    else if (s.equalsIgnoreCase("orange"))
      fgColor = Color.orange;
    else if (st.countTokens() == 3) {
      Integer r = new Integer(st.nextToken());
      Integer g = new Integer(st.nextToken());
      Integer b = new Integer(st.nextToken());
      fgColor = new Color(r.intValue(), g.intValue(), b.intValue());
    } else
      fgColor = Color.yellow;
		setBackground(bgColor);
		f = new Font(fontString, style, size);
		// Pour un placement correct du message dans la zone d'affichage
		FontMetrics fm = getFontMetrics(f);
		int textWidth = fm.stringWidth(text);
		x = (width - textWidth) / 2;
		int textHeight = fm.getAscent() + fm.getDescent();
		y = (height - textHeight) / 2 + fm.getAscent();
		offscreenImg = createImage(width, height);
		offscreenG = offscreenImg.getGraphics();
		offscreenG.setColor(bgColor);
		offscreenG.fillRect(0, 0, width, height);
		repaint();
	}
	public void start()
	{
		if (runner == null);
		{
			runner = new Thread(this);
			runner.start();
		}
	}
	public void stop()
	{
		if (runner != null)
		{
		runner.stop();
		runner = null;
		}
	}
	public void createBackground()
	{
		boolean switchColor;
		for (int i=0; i <= 5; i++)
		{
			fondEcran[i] = createImage(width, height);
			fondEcranG[i] = fondEcran[i].getGraphics();
			fondEcranG[i].setColor(bgColor);
			fondEcranG[i].fillRect(0, 0, width, height);
			switchColor = true;
			for (int j= max + ((i + 1) * (step / 3)); j >= 0; j -= step)
			{
				if (switchColor)
				{
					fondEcranG[i].setColor(bgColor);
					if (bgType.equals("Oval")) fondEcranG[i].fillOval((width - j) / 2, (height - j) / 2, j, j);
					if (bgType.equals("Rect")) fondEcranG[i].fillRect((width - j) / 2, (height - j) / 2, j, j);
					if (bgType.equals("RoundRect")) fondEcranG[i].fillRoundRect((width - j) / 2, (height - j) / 2, j, j, cornerX, cornerY);
					switchColor = false;
				}
				else
				{
					fondEcranG[i].setColor(fgColor);
					if (bgType.equals("Oval")) fondEcranG[i].fillOval((width - j) / 2, (height - j) / 2, j, j);
					if (bgType.equals("Rect")) fondEcranG[i].fillRect((width - j) / 2, (height - j) / 2, j, j);
					if (bgType.equals("RoundRect")) fondEcranG[i].fillRoundRect((width - j) / 2, (height - j) / 2, j, j, cornerX, cornerY);
					switchColor = true;
				}
			}
		}
	}
	public void run()
	{
		if (width > height)
				max = width;
		else max = height;
		max *= Math.sqrt(2);
		createBackground();
		repaint();
		offscreenG.setFont(f);
		offscreenG.setColor(textColor);
		while (true)
		{
			for (int i=0; i <= 5; i++)
			{
				offscreenG.drawImage(fondEcran[i], 0, 0, this);
				offscreenG.drawString(text, x, y);
				repaint();
				try { Thread.sleep(timeSleep); }
				catch (InterruptedException e) { }
			}
			if (playBack == 1)
			{
				for (int i=5; i >= 0; i--)
				{
					offscreenG.drawImage(fondEcran[i], 0, 0, this);
					offscreenG.drawString(text, x, y);
					try { Thread.sleep(timeSleep); }
					catch (InterruptedException e) { }
					repaint();
				}
			}
		}
	}
	public void update(Graphics g)
	{
		paint(g);
	}
	public void paint(Graphics g)
	{
		g.drawImage(offscreenImg, 0, 0, this);
	}
	public String getAppletInfo()
	{
		return "AnimCercles, april 1997, Vincent Zimmermann at NCTech, France";
	}
}
```

