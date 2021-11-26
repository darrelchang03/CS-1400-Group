------------------------------------------------------------AppClient.java----------------------------------
****Changed some some stuff from the code that he gave us. *****


package buildingSim;
/* This is a stub code. You can modify it as you wish. */

import java.io.*;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.util.InputMismatchException;
import java.util.Scanner;
import buildingSim.ApplianceGenerator.Appliance;

class AppClient{
	
	public void readAppFile(String file){ // method to read the comma seperated appliance file.
		Scanner scan;
		try {
			File myFile=new File(file);
			scan=new Scanner(myFile);//each line has the format
			//locationID,name of app,onPower,probability of staying on, smart or not,Smart appliances (if "on") power reduction percent when changed to "low" status(floating point, i.e..33=33%).
			String str;
			int locationID;
			String appName;
			int onPower;
			float probOn;
			String appType;
			int lowPower;
			Appliance aAppl;
			
			/*Complete the method*/
			scan.close();
		}catch(IOException ioe){ 
			System.out.println("The file can not be read");
			
			
		}
	}
	
	
	public static void main(String []args){
		
		
		AppClient app = new AppClient();
		//User interactive part
		String option1, option2;
		Scanner scnr = new Scanner(System.in);
		while(true){// Application menu to be displayed to the user.
			System.out.println("Select an option:");
			System.out.println("Type \"A\" Add an appliance");
			System.out.println("Type \"D\" Delete an appliance");	
			System.out.println("Type \"L\" List the appliances");
			System.out.println("Type \"F\" Read Appliances from a file");
			System.out.println("Type \"S\" To Start the simulation");
			System.out.println("Type \"Q\" Quit the program");
			option1=scnr.nextLine();
			
			//add an appliance
			if (option1.equals("A")) {
				boolean correct = false;
				String appName = "";
				int appLocation = 0;
				int onWatt = 0;
				int offWatt = 0;
				double probOn = 0.0;
				boolean ifSmart = false;
				double smartProb = 0.0;
				
				do {//check user input for name
					try {
						correct = true;
						System.out.println("Please enter the name of the appliance you would like to add.");
						appName = scnr.nextLine();
					}catch(InputMismatchException e) {
						scnr.nextLine();
						correct = false;
						System.out.println(e);
						System.out.println("");
					}
				}while (!correct);
				do { //check user input
					try{
						do {
							correct = true;
							System.out.println("Please enter the \"off\" wattage.");
							offWatt = scnr.nextInt();
						}while(offWatt<0);
					}catch(InputMismatchException e) {
						scnr.nextLine();
						System.out.println(e);
						System.out.println("");
						correct = false;
					}
				}while (!correct);
				do { //check user input
					try{
						do{
							correct = true;
							System.out.println("Please enter the \"on\" wattage.");
							onWatt = scnr.nextInt();
						}while (onWatt<0);
					}catch(InputMismatchException e) {
						scnr.nextLine();
						System.out.println(e);
						System.out.println("");
						correct = false;
					}
				}while (!correct);
				do { //check user input
					try{
						do {
							correct = true;
							System.out.println("Please enter the probability(0-1 inclusive) that the appliance will turn on.");
							probOn = scnr.nextDouble();
						}while(probOn>1 || probOn<0);
					}catch(InputMismatchException e) {
						scnr.nextLine();
						System.out.println(e);
						System.out.println("");
						correct = false;
					}
				}while (!correct);
				do { //check user input
					try{
						correct = true;
						System.out.println("Please specify if the appliance will be \"smart\" (true or false).");
						ifSmart = scnr.nextBoolean();
						if (ifSmart) {
							do { //check user input
								try { 
									do {
									correct = true;
									System.out.println("Please enter the power cut rate(0-1 inclusive) on the appliance when it is in low power mode.");
									smartProb = scnr.nextDouble();
									}while(smartProb>1 || smartProb<0);
								}catch(InputMismatchException e) {
									scnr.nextLine();
									System.out.println(e);
									System.out.println("");
									correct = false;
								}
							}while (!correct);
						}else {
							smartProb = 0.0;
						}
					}catch(InputMismatchException e) {
						scnr.nextLine();
						System.out.println(e);
						System.out.println("");
						correct = false;
					}
				}while (!correct);
				//write new appliance to file to "create" it
				try
				{
					FileWriter fw = new FileWriter( "ApplianceDetail.txt", true);
					BufferedWriter bw = new BufferedWriter( fw );
					bw.write(appName + ",");
					bw.write(onWatt + ",");
					bw.write(offWatt + ",");
					bw.write(probOn + ",");
					bw.write(ifSmart + ",");
					bw.write(String.valueOf(smartProb));
					bw.newLine();
					bw.flush();
					System.out.println("Item successfully added to inventory!");
				}
				catch(IOException ioe)
				{
					ioe.printStackTrace( );
				}
			}
			
			if (option1.equals("B")) {
				
			}
		}

	}
}

----------------------------------------------------------ApplianceGeneratorClass--------------------------------------------
******Changed the app[] array to an arrayList so that we can add appliance to the list without limit*****

package buildingSim;
import java.util.*;
import java.util.StringTokenizer;
import java.io.File;
import java.io.IOException;
import java.io.FileWriter;
import java.io.BufferedWriter;

public class ApplianceGenerator {
	public static ArrayList<Appliance>app;
	
	public static class Appliance {
		public String name;
		public int onW, offW; 
		public double probOn; 
		public boolean smart; 
		public double probSmart;
		

		public Appliance (String n, int on, int off, double pOn, boolean s, double pSmart)
		{ name=n; onW=on; offW=off; probOn=pOn; smart=s; probSmart=pSmart; }

		public String toString () {
			return name + "," + onW + "," + offW + "," + probOn + "," + smart + "," + probSmart;
		}
	}

	public static void main( String [] args ) throws IOException {
		//Appliance [] app = new Appliance[100];  // default 100 possible appliance types
		File inputFile = new File( "ApplianceDetail.txt" );
		Scanner scan = new Scanner( inputFile );
		int count=0;
		while ( scan.hasNext( ) ) {
			StringTokenizer stringToken = new StringTokenizer(scan.nextLine());
			app.add(new Appliance(stringToken.nextToken(","),
					Integer.parseInt(stringToken.nextToken(",")),
					Integer.parseInt(stringToken.nextToken(",")),
					Double.parseDouble(stringToken.nextToken(",")),
					Boolean.parseBoolean(stringToken.nextToken(",")),
					Double.parseDouble(stringToken.nextToken())));
			count++;
		}
/*
output a comma delimited file
the location (represented by an 8 digit numeric account number)
type (string)
"on" wattage used (integer)
probability (floating point, i.e..01=1%) that the appliance is "on" at any time
smart (boolean) 
Smart appliances (if "on") power reduction percent when changed to "low" status(floating point, i.e..33=33%).
*/
		try
		{
			FileWriter fw = new FileWriter( "output.txt", false);
			BufferedWriter bw = new BufferedWriter( fw );
			for (long location=1;location<=100 ;location++ ) {   // default 100 locations
				int applianceCount=(int)(Math.random()*6)+15;  //15-20 appliances per location 
				for (int i=1;i<=applianceCount;i++ ){
					int index=(int)(Math.random()*count);  // pick an appliance randomly
					bw.write(String.valueOf(10000000+location));
					bw.write( "," );		
					bw.write(app.get(index).name);
					bw.write( "," );		
					bw.write(String.valueOf(app.get(index).onW));
					bw.write( "," );									
					bw.write(String.valueOf(app.get(index).probOn));
					bw.write( "," );		
					bw.write(String.valueOf(app.get(index).smart));
					bw.write( "," );
					bw.write(String.valueOf(app.get(index).probSmart));
					bw.newLine( );
					bw.flush();
				}
			}
		}
		catch( IOException ioe )
		{
			ioe.printStackTrace( );
		}
	}
}
