------------------------------------------------------------AppClient.java----------------------------------
****Changed some some stuff from the code that he gave us. *****


package buildingSim;
/* This is a stub code. You can modify it as you wish. */

import java.io.*;
import java.util.ArrayList;
import java.util.InputMismatchException;
import java.util.List;
import java.util.Scanner;
import java.util.StringTokenizer;

import buildingSim.ApplianceGenerator.Appliance;

class AppClient {
	
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
			while(scan.hasNextLine()) {
				scan.useDelimiter(",");
				locationID = scan.nextInt();
				appName = scan.next();
				onPower = scan.nextInt();
				probOn = scan.nextFloat();
				appType = scan.next();
				lowPower = scan.nextInt();
				List.add(new Appliance(locationID, appName, onPower, probOn, appType, lowPower)); 
				// creates an object Appliance with file defined attributes values in the arraylist List (doesnt work until Arraylist code is fixed)
			}
			/*Complete the method*/
			scan.close();
		}catch(IOException ioe){ 
			System.out.println("The file can not be read");
			
			
		}
	}
	public static List<List<GeneratedAppliance>> addAppsFromFile(String fileName){
		List<GeneratedAppliance> apps = new ArrayList<GeneratedAppliance>();
		List<List<GeneratedAppliance>>appsByArea = new ArrayList<List<GeneratedAppliance>>();
		List<GeneratedAppliance> tempList = new ArrayList<GeneratedAppliance>();
		Long prevAppLocation = (long)10000001;
		try(Scanner scnr = new Scanner(new File (fileName))){
			while (scnr.hasNext()) {
				//add to arrayList
				StringTokenizer stringToken = new StringTokenizer(scnr.nextLine());
				Long currentAppLocation = Long.parseLong(stringToken.nextToken(","));
				String appName = stringToken.nextToken(",");
				int onWatt = Integer.parseInt(stringToken.nextToken(","));
				double probOn = Double.parseDouble(stringToken.nextToken(","));
				boolean isSmart = Boolean.parseBoolean(stringToken.nextToken(","));
				double smartProb = Double.parseDouble(stringToken.nextToken());
				if (currentAppLocation-prevAppLocation!=0 || currentAppLocation == 10000100) {
					for(int i=0; i<tempList.size();++i) {
						System.out.println(tempList.get(i).getAppName());
						System.out.println();
					}
					appsByArea.add(tempList);
					tempList.clear();
					
				}
				tempList.add(new GeneratedAppliance(currentAppLocation,appName,onWatt, probOn,isSmart, smartProb));
				apps.add(new GeneratedAppliance(currentAppLocation,appName,onWatt, probOn,isSmart, smartProb));
				
				prevAppLocation = currentAppLocation;
				
				
			}			
		}catch (IOException e) {
			System.out.println(e);
		}
		
		return appsByArea;
	}
	
	public static void main(String []args){
		boolean correct = false;
		String appName = "";
		long appLocation = 0;
		int onWatt = 0;
		int offWatt = 0;
		double probOn = 0.0;
		boolean ifSmart = false;
		double smartProb = 0.0;
		// 1D and 2D arrayList for sorting and searching Appliances
		List<GeneratedAppliance> appsList;
		List<List<GeneratedAppliance>> appsByArea;
		
		AppClient app = new AppClient();
		//User interactive part
		appsByArea = addAppsFromFile("app.txt");
		
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
							System.out.println("Please enter the location(1-100 inclusive) of the new appliance.");
							appLocation = scnr.nextInt();
						}while(appLocation<1 || appLocation>100);
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
				{//	NOTE TO SELF:  MAKE IT PRINT WITH SAME AREA CODE ITEMS
					FileWriter fw = new FileWriter( "app.txt", true);
					BufferedWriter bw = new BufferedWriter( fw );
					bw.write((appLocation + 10000000) + ",");
					bw.write(appName + ",");
					bw.write(onWatt + ",");
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
		//create menu option delete appliance
			if (option1.equals("D")) {
				
				do {//check user input for name
					try {
						correct = true;
						System.out.println("Please enter the name of the appliance you would like to delete.");
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
							System.out.println("Please enter the location(1-100 inclusive) of the new appliance.");
							appLocation = scnr.nextInt();
						}while(appLocation<1 || appLocation>100);
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
							System.out.println("Please enter the \"on\" wattage of the item you would like to delete.");
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
							System.out.println("Please enter the \"on\" probability(0-1 inclusive) of the item you would like to delete.");
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
						System.out.println("Please specify the \"smart\" factor(true or false) of the appliance you would like to delete.");
						ifSmart = scnr.nextBoolean();
						if (ifSmart) {
							do { //check user input
								try { 
									do {
									correct = true;
									System.out.println("Please enter the power cut rate(0-1 inclusive) of the appliance that you would like to delete.");
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
			if (option1.equals("F")) {
				try {
					String file;
					File myFile=new File(file);
					Scanner scan = new Scanner(myFile);//each line has the format
//locationID,name of app,onPower,probability of staying on, smart or not,Smart appliances (if "on") power reduction percent when changed to "low" status(floating point, i.e..33=33%).
					String str;
					int locationID;
					int onPower;
					String appType;
					int lowPower;
					Appliance aAppl;
					while(scan.hasNextLine()) {
						scan.useDelimiter(",");
						locationID = scan.nextInt();
						appName = scan.next();
						onPower = scan.nextInt();
						probOn = scan.nextFloat();
						appType = scan.next();
						lowPower = scan.nextInt();
						List.add(new Appliance(locationID, appName, onPower, probOn, appType, lowPower)); 
// creates an object Appliance with file defined attributes values in the arraylist List (doesnt work until Arraylist code is fixed)
						}		
						scan.close();
					}catch(IOException ioe){ 
						System.out.println("The file can not be read");
					}
				}
			}
			
		}

	}
}

----------------------------------------------------------ApplianceGeneratorClass---------------------------------

package buildingSim;

import java.util.*;
import java.util.StringTokenizer;
import java.io.File;
import java.io.IOException;
import java.io.FileWriter;
import java.io.BufferedWriter;

public class ApplianceGenerator {
	public Scanner scnr = new Scanner(System.in);
	
	public static class Appliance {
		public String name;
		public int onW, offW;
		public double probOn;
		public boolean smart;
		public double probSmart;

		public Appliance(String n, int on, int off, double pOn, boolean s, double pSmart) {
			name = n;
			onW = on;
			offW = off;
			probOn = pOn;
			smart = s;
			probSmart = pSmart;
		}

		public String toString() {
			return name + "," + onW + "," + offW + "," + probOn + "," + smart + "," + probSmart;
		}
	}

	public static void main(String[] args) throws IOException {
		Appliance[] appTypes = new Appliance[100]; // default 100 possible appliance types
		File inputFile = new File("ApplianceDetail.txt");
		Scanner scan = new Scanner(inputFile);
		int count = 0;
		while (scan.hasNext()) {
			StringTokenizer stringToken = new StringTokenizer(scan.nextLine());
			appTypes[count] = (new Appliance(stringToken.nextToken(","), 									Integer.parseInt(stringToken.nextToken(",")),
					Integer.parseInt(stringToken.nextToken(",")), 									Double.parseDouble(stringToken.nextToken(",")),
					Boolean.parseBoolean(stringToken.nextToken(",")), 								Double.parseDouble(stringToken.nextToken())));
			count++;
		}
		/*
		 * output a comma delimited file the location (represented by an 8 digit numeric
		 * account number) type (string) "on" wattage used (integer) probability
		 * (floating point, i.e..01=1%) that the appliance is "on" at any time smart
		 * (boolean) Smart appliances (if "on") power reduction percent when changed to
		 * "low" status(floating point, i.e..33=33%).
		 */
		try {
			FileWriter fw = new FileWriter("app.txt", false);
			BufferedWriter bw = new BufferedWriter(fw);
			for (long location = 1; location <= 100; location++) {// default 100 locations
				List<GeneratedAppliance> tempList = new ArrayList<GeneratedAppliance>();
				int applianceCount = (int) (Math.random() * 6) + 15; // 15-20 appliances per location
				for (int i = 1; i <= applianceCount; i++) {
					int index = (int) (Math.random() * count); // pick an appliance randomly
					bw.write(String.valueOf(10000000 + location));
					bw.write(",");
					bw.write(appTypes[index].name);
					bw.write(",");
					bw.write(String.valueOf(appTypes[index].onW));
					bw.write(",");
					bw.write(String.valueOf(appTypes[index].probOn));
					bw.write(",");
					bw.write(String.valueOf(appTypes[index].smart));
					bw.write(",");
					bw.write(String.valueOf(appTypes[index].probSmart));
					bw.newLine();
					bw.flush();
					
					/*
					 * addAppliance((10000000 + location), appTypes[index].name,
					 * appTypes[index].onW, appTypes[index].probOn, appTypes[index].smart,
					 * appTypes[index].probSmart); tempList.add(new GeneratedAppliance((10000000 +
					 * location), appTypes[index].name, appTypes[index].onW, appTypes[index].probOn,
					 * appTypes[index].smart, appTypes[index].probSmart));
					 */
					
				}
				//appsByArea.add(tempList);
			}
		} catch (IOException ioe) {
			ioe.printStackTrace();
		}
	}
	
	
---------------------------------------------------------GeneratedAppliance Class---------------------------------
package buildingSim;

public class GeneratedAppliance {
	private long appLocation;
	private String appName;
	private int onWatt;
	private double probOn;
	private boolean isSmart;
	private double smartProb;
	
	public  GeneratedAppliance(){
		this.appLocation = 0;
		this.appName = "";
		this.onWatt = 0;
		this.probOn = 0.0;
		this.isSmart = false;
		this.smartProb = 0.0;
	}
	
	public GeneratedAppliance (long myLocation, String myName, int myWatt, double myProb, boolean myIsSmart, double mySmartProb) {
		setAppLocation(myLocation);	setAppName(myName);	setOnWatt(myWatt);	setProbOn(myProb);	setIsSmart(myIsSmart);	setSmartProb(mySmartProb);
	}
	
	//mutators and getters
	public void setAppLocation(long myLocation) {
		this.appLocation = myLocation;
	}
	public void setAppName(String myName) {
		this.appName = myName;
	}
	public void setOnWatt(int myWatt) {
		this.onWatt = myWatt;
	}
	public void setProbOn(double myProb) {
		this.probOn = myProb;
	}
	public void setIsSmart(boolean mySmart) {
		this.isSmart = mySmart;
	}
	public void setSmartProb(double mySmartProb) {
		this.smartProb = mySmartProb;
	}
	public long getAppLocation() {
		return this.appLocation;
	}
	public String getAppName() {
		return this.appName;
	}
	public int getOnWatt() {
		return this.onWatt;
	}
	public double getProbOn() {
		return this.probOn;
	}
	public boolean getIsSmart() {
		return this.isSmart;
	}
	public double getSmartProb() {
		return this.smartProb;
	}
}

