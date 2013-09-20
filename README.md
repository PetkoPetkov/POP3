
import java.io.*;
import java.net.*;
import javax.swing.*;
import java.awt.event.*;
import java.awt.*;
import javax.swing.JOptionPane;

 public class Login	 extends JApplet {

	 	String  SERVER 	= "mail.bg";
	    int 	PORT = 110;	
	 	String  USER; 	//"your@mail.bg";
	    String  PASS; //= ".........";							//"parola";
	   	BufferedReader 		in;
	    PrintWriter 		out;
	    Socket  			s;
	    
	// Променлива за панела
	    			JPanel  panelObject; 
	    			
	// Променливи за етикетите

					JButton buttonClear;
					JButton login;
					JLabel  labelCustPass;
					JLabel  labelCustName;
					JLabel  labelCustVuvedi;
				
	// Променливи на контролите за въвеждане на данни
					JPasswordField  textCustPass;
					JTextField 		textCustName;
					TextArea			textArea;
					JTextField 		textCustVuvedi;
		
	// Променливи за подредбата
					GridBagLayout      gbObject;
					GridBagConstraints gbc;

	public   void init() {
			gbObject = new GridBagLayout();
			gbc = new GridBagConstraints();
			panelObject = (JPanel)getContentPane();
			panelObject.setLayout(gbObject);
			buttonClear = new JButton("Clear");
			login = new JButton("Login");
		
			labelCustPass = new JLabel("Pass");
			labelCustName = new JLabel("Name");
			labelCustVuvedi = new JLabel("Filter e-mail by keyword");
			textArea 	  = new TextArea(" Тук ще се показва съдържанието на съобщенията", 22, 66);	
			
		// Инсталация на контролите за въвеждане на данни
			textCustPass = new JPasswordField(11);
 			textCustName = new JTextField(11);
 			textCustVuvedi = new JTextField(11);
 			
							gbc.anchor=GridBagConstraints.CENTER;
							gbc.insets = new Insets(30,0,0,20);
							gbc.gridx=1;
							gbc.gridy=5;
							gbObject.setConstraints(labelCustVuvedi,gbc);
							panelObject.add(labelCustVuvedi);
							gbc.anchor=GridBagConstraints.CENTER;
							gbc.gridx=2;
							gbc.gridy=5;
							gbObject.setConstraints(textCustVuvedi,gbc);
							panelObject.add(textCustVuvedi);
							
			//----------------------------------------------------------------------------------------------------------------------
			
		// Добавяне на контролите за потребителско име
			gbc.anchor=GridBagConstraints.CENTER;
			gbc.insets = new Insets(20,10,10,10);
			gbc.gridx=8;
			gbc.gridy=7;
			gbObject.setConstraints(labelCustName,gbc);
			panelObject.add(labelCustName);
			gbc.anchor=GridBagConstraints.WEST;
			gbc.gridx=9;
			gbc.gridy=7;
			gbObject.setConstraints(textCustName,gbc);
			panelObject.add(textCustName);
			
		// Добавяне на контролите за парола
			gbc.anchor=GridBagConstraints.CENTER;
			gbc.insets = new Insets(10,10,10,10);
			gbc.gridx=8;
			gbc.gridy=8;
			gbObject.setConstraints(labelCustPass,gbc);
			panelObject.add(labelCustPass);
			gbc.anchor=GridBagConstraints.WEST;
			gbc.gridx=9;
			gbc.gridy=8;
			gbObject.setConstraints(textCustPass,gbc);
			panelObject.add(textCustPass);
						
		// Добавяне на контролите за бутоните  "Login" и "Clear"
			gbc.anchor=GridBagConstraints.CENTER;
			gbc.insets = new Insets(10,20,20,20);
			gbc.gridx=9;
			gbc.gridy=8;
			gbObject.setConstraints(login,gbc);
			panelObject.add(login);
			ValidateAction validateButton = new ValidateAction();
			login.addActionListener(validateButton);
			 
			gbc.anchor=GridBagConstraints.CENTER;
			gbc.gridx=9;
			gbc.gridy=7;
			gbObject.setConstraints(buttonClear,gbc);
			panelObject.add(buttonClear);
			buttonClear.addActionListener(validateButton);
			
			// Добавяне на контролите за текстовото поле
			
			gbc.gridx=9;
			gbc.gridy=1;
			gbc.gridheight=6;
			gbc.insets = new Insets(5,20,0,0);
			gbObject.setConstraints(textArea,gbc);
			panelObject.add(textArea);
			
	} 
		
	// Клас за обслужване на събитията генерирани от бутоните
	class ValidateAction implements ActionListener 	 {
		public void actionPerformed(ActionEvent evt) {
		// Извличане на източника на събитието
			Object obj = evt.getSource();
			
			if(obj == buttonClear) {
				// Извличане на номера на текстовото поле
				textCustPass.setText("");
				textCustName.setText("");
				textArea.setText("");
				textCustVuvedi.setText("");
				getAppletContext().showStatus("Моля въведете име и парола");			
			} 
			
			if(obj == login) {
				
				USER = textCustName.getText();
				PASS = textCustPass.getText();
				
				if(USER.length() == 0 || PASS.length() == 0)
					JOptionPane.showMessageDialog(null, "Неможе да има празно поле");
				else if(USER.length() < 5 || PASS.length() < 5)
					JOptionPane.showMessageDialog(null,"Дължината на полето трябва да е минимум 5 символа");
		 		if(USER.length() >= 5 && PASS.length() >= 5)
//		 			box();
				sendLoginInfo();
		 		//JOptionPane.showMessageDialog(null,"Finish");
				getAppletContext().showStatus("Finish");
			}	
		}
	}  
		// Комуникация с WEB сървъра
	private   void sendLoginInfo() {
		try {
			 InetAddress ia = InetAddress.getByName(SERVER);
			 s   = new Socket(ia, PORT);
			 in  = new BufferedReader (new InputStreamReader (s.getInputStream() ));
			 out = new PrintWriter ( new BufferedWriter (new OutputStreamWriter (s.getOutputStream() )), true);
			 JOptionPane.showMessageDialog(null,"Connecting...");
			 receive();
			 
			boolean regStatus = register();
			if (!regStatus) {
				JOptionPane.showMessageDialog(null,"Can`t register");
				return;	
			}
			if(regStatus) {
				textCustPass.setText("");
				textCustName.setText("");
				
			}
			int numOfMessages = getNumOfMessages();
			if (numOfMessages == 0) {
				JOptionPane.showMessageDialog(null,"No Message ");
				return;	
			} else
				JOptionPane.showMessageDialog(null,"There are" +numOfMessages+"  messages");
			for (int i=0; i<numOfMessages; i++) {
				JOptionPane.showMessageDialog(null,"This is a iformation for "+(i+1)+"  message");
						
						readMessage(i+1);
//				deleteMessage(i+1);
			}
			
			send("QUIT");
			receive();
		}catch (IOException ace){ }
		 catch (Exception e) {
			if (s == null) System.out.println("Server not responding!");
			else JOptionPane.showMessageDialog(null,"Comunication error!");	
		} finally {
			try {
				if (s != null) s.close();
			} catch(IOException ioe) {}	
		 }
	}
	// -------------------------------------------------------------------------
	
		// Изпраща информация към сървара
	  void send(String command) throws IOException {
		getAppletContext().showStatus(command);
		out.println(command);
	}
	// -------------------------------------------------------------------------
	  	// Връща отговора на сървара
	  String receive() throws IOException {
		String answer;
		answer = in.readLine();
		getAppletContext().showStatus(answer);
	//koment za answer
		return answer;
	}
	// -------------------------------------------------------------------------
	  	// Използва регистрация чрез "USER" и "PASS"
	  boolean register() throws IOException {
		String line;
		send("USER "+USER);
		line = receive();
		if (line.indexOf("+") == -1)
			return false;
		send("PASS "+PASS);
		line = receive();
		if (line.indexOf("+") == -1)
			return false;
			return true;	
	}
	// -------------------------------------------------------------------------
	  	// Връща броя на съобщенията
	  int getNumOfMessages() throws IOException {
		send("STAT");
		String line = receive();
		String[] data = line.split(" ");
		return Integer.parseInt(data[1]);
	}
	// -------------------------------------------------------------------------
	  	// Разпечатва заглавния блок и първите два реда от тялото на указаното с номер съобщение,
	  	//при условие че съдържа само текст
	  
 public	  void readMessage(int number) throws IOException {
	 
	
//	 String select = comboBox.getSelectedItem().toString();
		
	 String vuvedi = textCustVuvedi.getText();
		if(vuvedi.equals("")){
			
			String line;
		//		boolean showBody = false;
			//	send("TOP "+number+2);
				send("RETR " +number);
				
				textArea.append(number + "-----------------------------------");
	//				while(!((line=in.readLine()).equals(""))) {
	//				if(line.indexOf("puhiny@mail.bg") != -1) 
	//					showBody = true;
	//			}  

				while(!((line=in.readLine()).equals("."))) {
		//			if(showBody) 
						textArea.append(line + "\n");
				}
			
		} else
		{
		
					//String prom = textCustVuvedi.getText()
				//	if(textCustVuvedi.equals("Specify")){
								
								String line;
									boolean showBody4 = false;
								//	send("TOP "+number+2);
									send("RETR " +number);
							//		String vuvedi = textCustVuvedi.getText();
									textArea.append(number + "-----------------------------------");
										while(!((line=in.readLine()).equals(""))) {
											
									//	if(line.indexOf("puhiny@abv.bg") != -1) 
									//		showBody4 = true;
										if(line.indexOf(vuvedi) != -1) 
											showBody4 = true;
									}  
										
									while(!((line=in.readLine()).equals("."))) {
										if(showBody4) 
											textArea.append(line +  "\n");
									}
								
						//	}
		
		}
	}	 
 				//-----------------------------------------------------------------------------------------------------------------------------
}
