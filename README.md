# 23BCS13917

/* ==========================================================
   PBLJ Java Applications Using JDBC and MVC
   Part A + Part B + Part C Combined Code
   Database: MySQL  (Database Name: pbljdb)
   ========================================================== */

/* ---------------------- PART A -------------------------
   Connecting to MySQL and Fetching Data from Users Table
   ------------------------------------------------------ */
import java.sql.*;

class FetchData {
    public static void main(String[] args) {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection con = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306/pbljdb", 
                    "root", 
                    "yourpassword"
            );
            Statement st = con.createStatement();
            ResultSet rs = st.executeQuery("SELECT * FROM users");
            System.out.println("USER DATA:");
            while (rs.next()) {
                System.out.println(
                    rs.getInt("id")+"  "+
                    rs.getString("username")+"  "+
                    rs.getString("password")
                );
            }
            con.close();
        } 
        catch (Exception e) {
            System.out.println(e);
        }
    }
}


/* ---------------------- PART B -------------------------
   CRUD Operations on Product Table using JDBC
   ------------------------------------------------------ */
import java.util.*;

class ProductCRUD {
    static Connection con;
    static Scanner sc = new Scanner(System.in);

    static void connect() throws Exception {
        Class.forName("com.mysql.cj.jdbc.Driver");
        con = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/pbljdb","root","yourpassword");
    }

    static void insertProduct() throws Exception {
        System.out.print("Enter Product ID: ");
        int id = sc.nextInt();
        System.out.print("Enter Product Name: ");
        String name = sc.next();
        System.out.print("Enter Price: ");
        double price = sc.nextDouble();

        PreparedStatement ps = con.prepareStatement("INSERT INTO product VALUES(?,?,?)");
        ps.setInt(1,id);
        ps.setString(2,name);
        ps.setDouble(3,price);
        ps.executeUpdate();
        System.out.println("Inserted Successfully");
    }

    static void updateProduct() throws Exception {
        System.out.print("Enter Product ID to Update Price: ");
        int id = sc.nextInt();
        System.out.print("Enter New Price: ");
        double price = sc.nextDouble();

        PreparedStatement ps = con.prepareStatement("UPDATE product SET price=? WHERE pid=?");
        ps.setDouble(1,price);
        ps.setInt(2,id);
        ps.executeUpdate();
        System.out.println("Updated Successfully");
    }

    static void deleteProduct() throws Exception {
        System.out.print("Enter Product ID to Delete: ");
        int id = sc.nextInt();

        PreparedStatement ps = con.prepareStatement("DELETE FROM product WHERE pid=?");
        ps.setInt(1,id);
        ps.executeUpdate();
        System.out.println("Deleted Successfully");
    }

    static void displayProducts() throws Exception {
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery("SELECT * FROM product");
        System.out.println("PRODUCT LIST:");
        while (rs.next()) {
            System.out.println(
                rs.getInt(1)+" "+rs.getString(2)+" "+rs.getDouble(3)
            );
        }
    }

    public static void main(String[] args) throws Exception {
        connect();
        while(true) {
            System.out.println("\n1.Insert  2.Update  3.Delete  4.Display  5.Exit");
            System.out.print("Enter Choice: ");
            int ch = sc.nextInt();
            switch(ch) {
                case 1: insertProduct(); break;
                case 2: updateProduct(); break;
                case 3: deleteProduct(); break;
                case 4: displayProducts(); break;
                case 5: return;
            }
        }
    }
}


/* ---------------------- PART C -------------------------
   Student Management System Using JDBC + MVC
   Model + DAO + View + Controller
   ------------------------------------------------------ */

// MODEL
class Student {
    private int roll;
    private String name;
    private String course;

    Student(int roll, String name, String course) {
        this.roll = roll;
        this.name = name;
        this.course = course;
    }
    int getRoll() { return roll; }
    String getName() { return name; }
    String getCourse() { return course; }
}

// DAO
class StudentDAO {
    Connection con;

    StudentDAO() throws Exception {
        Class.forName("com.mysql.cj.jdbc.Driver");
        con = DriverManager.getConnection(
            "jdbc:mysql://localhost:3306/pbljdb","root","yourpassword");
    }

    void addStudent(Student s) throws Exception {
        PreparedStatement ps = con.prepareStatement(
            "INSERT INTO student VALUES(?,?,?)"
        );
        ps.setInt(1, s.getRoll());
        ps.setString(2, s.getName());
        ps.setString(3, s.getCourse());
        ps.executeUpdate();
    }

    void viewStudents() throws Exception {
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery("SELECT * FROM student");
        System.out.println("STUDENT DETAILS:");
        while (rs.next()) {
            System.out.println(
                rs.getInt(1)+"  "+rs.getString(2)+"  "+rs.getString(3)
            );
        }
    }
}

// VIEW
class StudentView {
    Scanner sc = new Scanner(System.in);

    Student getStudentDetails() {
        System.out.print("Enter Roll No: ");
        int roll = sc.nextInt();
        System.out.print("Enter Name: ");
        String name = sc.next();
        System.out.print("Enter Course: ");
        String course = sc.next();

        return new Student(roll, name, course);
    }

    int menu() {
        System.out.println("\n1.Add Student  2.View Students  3.Exit");
        System.out.print("Choice: ");
        return sc.nextInt();
    }
}

// CONTROLLER (Main)
class StudentController {
    public static void main(String[] args) throws Exception {
        StudentDAO dao = new StudentDAO();
        StudentView view = new StudentView();

        while(true) {
            int ch = view.menu();
            switch(ch) {
                case 1: dao.addStudent(view.getStudentDetails());
                        System.out.println("Student Added.");
                        break;

                case 2: dao.viewStudents();
                        break;

                case 3: return;
            }
        }
    }
}
