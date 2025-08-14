# Employee-Database-App

import java.sql.*;
import java.util.Scanner;

public class EmployeeDatabaseApp {
    
    private static final String URL = "jdbc:mysql://localhost:3306/company_db";
    private static final String USER = "root"; 
    private static final String PASSWORD = "root"; 

    private static Connection conn;
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        try {
            
            Class.forName("com.mysql.cj.jdbc.Driver");

            
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            System.out.println("Connected to database successfully.");

            int choice;
            do {
                System.out.println("\n--- Employee Database Menu ---");
                System.out.println("1. Add Employee");
                System.out.println("2. View Employees");
                System.out.println("3. Update Employee");
                System.out.println("4. Delete Employee");
                System.out.println("5. Exit");
                System.out.print("Enter choice: ");
                choice = scanner.nextInt();
                scanner.nextLine(); 

                switch (choice) {
                    case 1 -> addEmployee();
                    case 2 -> viewEmployees();
                    case 3 -> updateEmployee();
                    case 4 -> deleteEmployee();
                    case 5 -> System.out.println("Exiting... Goodbye!");
                    default -> System.out.println("Invalid choice! Try again.");
                }
            } while (choice != 5);

            conn.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    
    private static void addEmployee() throws SQLException {
        System.out.print("Enter Name: ");
        String name = scanner.nextLine();

        System.out.print("Enter Position: ");
        String position = scanner.nextLine();

        System.out.print("Enter Salary: ");
        double salary = scanner.nextDouble();

        String sql = "INSERT INTO employees (name, position, salary) VALUES (?, ?, ?)";
        PreparedStatement pstmt = conn.prepareStatement(sql);
        pstmt.setString(1, name);
        pstmt.setString(2, position);
        pstmt.setDouble(3, salary);

        int rows = pstmt.executeUpdate();
        System.out.println(rows > 0 ? " Employee added successfully." : " Failed to add employee.");
    }

    
    private static void viewEmployees() throws SQLException {
        String sql = "SELECT * FROM employees";
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery(sql);

        System.out.println("\n--- Employee List ---");
        while (rs.next()) {
            System.out.printf("ID: %d | Name: %s | Position: %s | Salary: %.2f\n",
                    rs.getInt("id"),
                    rs.getString("name"),
                    rs.getString("position"),
                    rs.getDouble("salary"));
        }
    }

    
    private static void updateEmployee() throws SQLException {
        System.out.print("Enter Employee ID to update: ");
        int id = scanner.nextInt();
        scanner.nextLine();

        System.out.print("Enter new Name: ");
        String name = scanner.nextLine();

        System.out.print("Enter new Position: ");
        String position = scanner.nextLine();

        System.out.print("Enter new Salary: ");
        double salary = scanner.nextDouble();

        String sql = "UPDATE employees SET name=?, position=?, salary=? WHERE id=?";
        PreparedStatement pstmt = conn.prepareStatement(sql);
        pstmt.setString(1, name);
        pstmt.setString(2, position);
        pstmt.setDouble(3, salary);
        pstmt.setInt(4, id);

        int rows = pstmt.executeUpdate();
        System.out.println(rows > 0 ? " Employee updated successfully." : " Employee not found.");
    }

    
    private static void deleteEmployee() throws SQLException {
        System.out.print("Enter Employee ID to delete: ");
        int id = scanner.nextInt();

        String sql = "DELETE FROM employees WHERE id=?";
        PreparedStatement pstmt = conn.prepareStatement(sql);
        pstmt.setInt(1, id);

        int rows = pstmt.executeUpdate();
        System.out.println(rows > 0 ? " Employee deleted successfully." : "Employee not found.");
    }
}
