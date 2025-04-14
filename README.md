
import java.util.*;

class ElectricityBillCalculator {

    static final double COST_PER_UNIT_CASE1 = 7.0;
    static final double COST_PER_UNIT_CASE2 = 3.85;
    static final double DEMAND_FACTOR = 0.7; 

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter the number of floors: ");
        int floors = scanner.nextInt();

        System.out.print("Enter the number of different appliances: ");
        int numAppliances = scanner.nextInt();
        scanner.nextLine();

        String[] appliances = new String[numAppliances];
        double[][][] powerConsumption = new double[floors][numAppliances][3];
        int[][] applianceCount = new int[floors][numAppliances];

        System.out.println("Enter the names of appliances:");
        for (int i = 0; i < numAppliances; i++) {
            System.out.print("Appliance " + (i + 1) + ": ");
            appliances[i] = scanner.nextLine();
        }

        for (int i = 0; i < floors; i++) {
            System.out.println("\nEnter power consumption details for Floor " + (i + 1) + ":");
            for (int j = 0; j < numAppliances; j++) {
                System.out.print(appliances[j] + " power (W): ");
                powerConsumption[i][j][0] = scanner.nextDouble() / 1000;

                while (true) {
                    System.out.print(appliances[j] + " usage (hours/day): ");
                    double hours = scanner.nextDouble();
                    if (hours <= 24) {
                        powerConsumption[i][j][1] = hours;
                        break;
                    }
                    System.out.println("Enter a valid input.");
                }

                while (true) {
                    System.out.print(appliances[j] + " days used per week: ");
                    double days = scanner.nextDouble();
                    if (days <= 7) {
                        powerConsumption[i][j][2] = days;
                        break;
                    }
                    System.out.println("Enter a valid input.");
                }
            }
        }

        System.out.println("\nEnter the number of appliances for each floor:");
        for (int i = 0; i < floors; i++) {
            System.out.println("Floor " + (i + 1) + ":");
            for (int j = 0; j < numAppliances; j++) {
                System.out.print(appliances[j] + " count: ");
                applianceCount[i][j] = scanner.nextInt();
            }
        }

        double totalBlockUnits = 0, totalConnectedLoad = 0, totalMaxDemand = 0;

        for (int i = 0; i < floors; i++) {
            double floorUnits = 0, connectedLoad = 0;

            System.out.println("\nElectrical Parameters for Floor " + (i + 1) + ":");
            System.out.println("+-----------------+------------+----------+----------+-----------+------------+------------+");
            System.out.printf("| %-15s | %-10s | %-8s | %-8s | %-9s | %-10s | %-10s |\n",
                    "Appliance", "Power (kW)", "Count", "Hours/Day", "Days/Week", "Monthly (kWh)", "Yearly (kWh)");
            System.out.println("+-----------------+------------+----------+----------+-----------+------------+------------+");

            for (int j = 0; j < numAppliances; j++) {
                double monthlyConsumption = powerConsumption[i][j][0] * powerConsumption[i][j][1] *
                        powerConsumption[i][j][2] * 4.33 * applianceCount[i][j];
                double yearlyConsumption = monthlyConsumption * 12;

                System.out.printf("| %-15s | %-10.2f | %-8d | %-8.2f | %-9.2f | %-10.2f | %-10.2f |\n",
                        appliances[j], powerConsumption[i][j][0], applianceCount[i][j], powerConsumption[i][j][1],
                        powerConsumption[i][j][2], monthlyConsumption, yearlyConsumption);

                floorUnits += monthlyConsumption;
                connectedLoad += powerConsumption[i][j][0] * applianceCount[i][j];
            }

            System.out.println("+-----------------+------------+----------+----------+-----------+------------+------------+");

            double totalTimeHours = 30 * 24;
            double maxDemand = connectedLoad * DEMAND_FACTOR;
            double avgLoad = floorUnits / totalTimeHours;
            double loadFactor = floorUnits / (maxDemand * totalTimeHours);
            double utilizationFactor = maxDemand / connectedLoad;
            double demandFactor = maxDemand / connectedLoad;

            double totalMonthlyCostCase1 = floorUnits * COST_PER_UNIT_CASE1;
            double totalYearlyCostCase1 = totalMonthlyCostCase1 * 12;

            double totalMonthlyCostCase2 = floorUnits * COST_PER_UNIT_CASE2;
            double totalYearlyCostCase2 = totalMonthlyCostCase2 * 12;

            System.out.printf("Total Units: %.2f kWh\n", floorUnits);
            System.out.printf("Connected Load: %.2f kW\n", connectedLoad);
            System.out.printf("Maximum Demand: %.2f kW\n", maxDemand);
            System.out.printf("Average Load: %.2f kW\n", avgLoad);
            System.out.printf("Demand Factor: %.2f\n", demandFactor);
            System.out.printf("Load Factor: %.2f\n", loadFactor);
            System.out.printf("Utilization Factor: %.2f\n", utilizationFactor);

            System.out.printf("Total Monthly Cost (Case 1 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE1, totalMonthlyCostCase1);
            System.out.printf("Total Yearly Cost (Case 1 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE1, totalYearlyCostCase1);
            System.out.printf("Total Monthly Cost (Case 2 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE2, totalMonthlyCostCase2);
            System.out.printf("Total Yearly Cost (Case 2 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE2, totalYearlyCostCase2);

            totalBlockUnits += floorUnits;
            totalConnectedLoad += connectedLoad;
            totalMaxDemand += maxDemand;
        }

        double totalBlockBillCase1 = totalBlockUnits * COST_PER_UNIT_CASE1;
        double totalBlockBillCase2 = totalBlockUnits * COST_PER_UNIT_CASE2;

        System.out.println("\nTotal Electricity Cost of Entire Block:");
        System.out.printf("Monthly Cost (Case 1 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE1, totalBlockBillCase1);
        System.out.printf("Monthly Cost (Case 2 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE2, totalBlockBillCase2);
        System.out.printf("Yearly Cost (Case 1 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE1, totalBlockBillCase1 * 12);
        System.out.printf("Yearly Cost (Case 2 @ Rs. %.2f/unit): Rs. %.2f\n", COST_PER_UNIT_CASE2, totalBlockBillCase2 * 12);

        scanner.close();
    }
}
