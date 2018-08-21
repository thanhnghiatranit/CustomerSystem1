# CustomerSystem1
package fjs.cs.action;

import java.io.IOException;
import java.util.Date;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import fjs.cs.dao.MSTCUSTOMER;
import fjs.cs.dao.MSTCUSTOMER_DAO;

public class CustomerAction extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {
		MSTCUSTOMER_DAO dao = new MSTCUSTOMER_DAO();
		String action = req.getParameter("action");
		if (action=="delete") {
			// // tra ve id da chon o checkbox 22022,22023
			String strList = req.getParameter("listDelete");
			// tra ve mang id
			String[] listId = strList.split(",");
			// [22022,22023]
			for (String id : listId) {
				dao.DeleteByID(id);
			}
			resp.sendRedirect("jsp/T002.jsp");
		}
		if(action=="create"){	
			MSTCUSTOMER mstcustomer = new MSTCUSTOMER() {
			};
			String customerName = req.getParameter("CUSTOMER_NAME");

			String sex = req.getParameter("SEX");
			String address = req.getParameter("ADDRESS"); //
			String email = req.getParameter("EMAIL"); //
			String birthday=req.getParameter("BIRTHDAY");
			Date UPDATE_YMD = new Date(req.getParameter("UPDATE_YMD")); //
			Date INSERT_YMD = new Date(req.getParameter("INSERT_YMD")); //
			Date DELETE_YMD = new Date(req.getParameter("DELETE_YMD"));
			mstcustomer.setCustomer_Name(customerName);
			mstcustomer.setSex(sex);
			mstcustomer.setAddress(address); // 
			mstcustomer.setBirthday(birthday);
			System.out.println(mstcustomer.toString());
			if (dao.AddNew(mstcustomer)) {
				System.out.println("OKkkkkkkkkkk");
			}
			resp.sendRedirect("jsp/T002.jsp");
			System.out.println("Zo");
		}
		if(action=="update"){
		}
	}
}

/**
 * @(#)T001.java 2017/09/14.
 *
 * Copyright(C) 2016 by FUJINET CO., LTD.
 *
 * Last_Update 2017/09/14.
 * Version 1.00.
 */
package fjs.cs.action;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import fjs.cs.common.Constants;
import fjs.cs.dao.MSTUSER_DAO;

/**
 * T001(Login)
 * 
 * @author TIEN-NTM
 * @version 1.00
 * 
 */
public class Login extends HttpServlet {

	private static final long serialVersionUID = 1L;

	/**
	 * Init man hinh
	 * 
	 * @param HttpServletRequest
	 *            req
	 * @param HttpServletResponse
	 *            resp
	 * @return RequestDispatcher
	 */
	protected void doGet(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {
		RequestDispatcher myRD = null;
		// Hien thi man hinh Login
		myRD = req.getRequestDispatcher(Constants.T001_LOGIN);
		myRD.forward(req, resp);
	}

	/**
	 * Event man hinh
	 * 
	 * @param HttpServletRequest
	 *            req
	 * @param HttpServletResponse
	 *            resp
	 * @return RequestDispatcher
	 */
	protected void doPost(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {
	
			// tao session
			HttpSession session = req.getSession();
			PrintWriter out = resp.getWriter();
			String username = req.getParameter("txtUserID");
			String password = req.getParameter("txtPassword");
			// xac thuc id va pass
			if (MSTUSER_DAO.validate(username, password)) {
				session.setAttribute("txtUserID", username);
				RequestDispatcher rd = req
						.getRequestDispatcher(Constants.T002_SEARCH);
				rd.forward(req, resp);
			} else {
				RequestDispatcher rd = req
						.getRequestDispatcher(Constants.T001_LOGIN);
				rd.forward(req, resp);
			} 
		
		
//			HttpSession session = req.getSession();
//			PrintWriter out = resp.getWriter();
//			req.getRequestDispatcher(Constants.T001_LOGIN).include(req, resp);
//			resp.sendRedirect("jsp/T001.jsp");
//			session.invalidate();
//			out.close();

		
	}
}

package fjs.cs.common;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class JdbcSQLServerConnection {

	private static String dbURL = "jdbc:sqlserver://localhost:1433;"
			+ "databaseName=CustomerSystem";
	private static String userName = "sa";
	private static String password = "Abc12345";

	   public static Connection getConnection() {
	        Connection conn = null;
	        try {
	        	//khai bao class Driver
	            Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	            conn = DriverManager.getConnection(dbURL, userName, password);
	        } catch (Exception ex) {
	            ex.printStackTrace();
	        }
	        return conn;
	    }
	public static void main(String[] args) {
		System.out.println(getConnection());
		
	}
}


package fjs.cs.dao;

import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

import javax.servlet.http.HttpSession;

import fjs.cs.common.JdbcSQLServerConnection;

public class MSTCUSTOMER_DAO {

	//ham lay ra 1 customer de hien thi len table trang search
	public ArrayList<MSTCUSTOMER> getListCustomer() {
		Connection conn = JdbcSQLServerConnection.getConnection();
		String sql = "SELECT * FROM MSTCUSTOMER";
		ArrayList<MSTCUSTOMER> list = new ArrayList<MSTCUSTOMER>();
		try {
			PreparedStatement ps = (PreparedStatement) conn
					.prepareStatement(sql);
			ResultSet rs = ps.executeQuery();
			while (rs.next()) {
				MSTCUSTOMER mstcustomer = new MSTCUSTOMER() {
				};
				mstcustomer.setCustomer_ID(rs.getString("CUSTOMER_ID"));
				mstcustomer.setCustomer_Name(rs.getString("CUSTOMER_NAME"));
				mstcustomer.setSex(rs.getString("SEX"));
				mstcustomer.setBirthday(rs.getString("BIRTHDAY"));
				mstcustomer.setAddress(rs.getString("ADDRESS"));
				list.add(mstcustomer);
			}
			conn.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return list;
	}

	//ham lay 1 customer theo id de hien thi o trang edit
	public ArrayList<MSTCUSTOMER> getListCustomerByID(String id) {
		Connection conn = JdbcSQLServerConnection.getConnection();
		String sql = "SELECT * FROM MSTCUSTOMER WHERE CUSTOMER_ID=?";
		ArrayList<MSTCUSTOMER> list = new ArrayList<MSTCUSTOMER>();
		try {
			PreparedStatement ps = conn.prepareStatement(sql);

			ps.setString(1, id);
			ResultSet rs = ps.executeQuery();
			while (rs.next()) {
				MSTCUSTOMER mstcustomer = new MSTCUSTOMER() {
				};
				mstcustomer.setCustomer_ID((rs.getString("CUSTOMER_ID")));
				mstcustomer.setCustomer_Name(rs.getString("CUSTOMER_NAME"));
				mstcustomer.setSex(rs.getString("SEX"));
				mstcustomer.setBirthday(rs.getString("BIRTHDAY"));
				mstcustomer.setAddress(rs.getString("ADDRESS"));
				mstcustomer.setEmail(rs.getString("EMAIL"));
				mstcustomer.setDelete_YMD(rs.getDate("DELETE_YMD"));
				mstcustomer.setInsert_YMD(rs.getDate("INSERT_YMD"));
				mstcustomer.setUpdate_YMD(rs.getDate("UPDATE_YMD"));
				list.add(mstcustomer);
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return list;

	}

	public boolean DeleteByID(String id) {

		Connection conn = JdbcSQLServerConnection.getConnection();
		String sql = "DELETE FROM MSTCUSTOMER WHERE CUSTOMER_ID=?";
		PreparedStatement ps;
		try {
			ps = conn.prepareStatement(sql);
			ps.setString(1, id);
			if (ps.executeUpdate() != 0)
				return true;
			else
				return false;
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return false;

	}

	public boolean AddNew(MSTCUSTOMER mstcustomer) {
		try {
			Connection conn = JdbcSQLServerConnection.getConnection();
			String sql = "INSERT INTO MSTCUSTOMER(CUSTOMER_NAME, SEX, ADDRESS, BIRTHDAY, EMAIL) VALUES(?,?,?,?,?)";
			// String sql =
			// "INSERT INTO MSTCUSTOMER(CUSTOMER_NAME, SEX, ADDRESS) VALUES(?,?,?)";
			PreparedStatement ps = (PreparedStatement) conn
					.prepareStatement(sql);

			ps.setString(1, mstcustomer.getCustomer_Name());
			ps.setString(2, mstcustomer.getSex());
			ps.setString(3, mstcustomer.getAddress());
			ps.setString(4, mstcustomer.getBirthday());
			ps.setString(4, mstcustomer.getEmail());
			// ps.setDate(6, cus.getD_updateYMD());
			// ps.setDate(7, cus.getD_insertYMD());
			// ps.setDate(8, cus.getD_deleteYMD());

			if (ps.executeUpdate() != 0)
				return true;
			else
				return false;

		} catch (SQLException e) {
			e.printStackTrace();
			return false;
		}
	}

	// phan trang
	// Pages number 1->4 or 5->8 ..
	public ArrayList<MSTCUSTOMER> getCustomerPT(int start, int sl) {
		Connection conn = JdbcSQLServerConnection.getConnection();
		ArrayList<MSTCUSTOMER> list = new ArrayList();
		String sql = "SELECT * FROM MSTCUSTOMER ORDER BY CUSTOMER_ID OFFSET ? ROWS FETCH NEXT ? ROWS ONLY";
		try {
			PreparedStatement ps = conn.prepareStatement(sql);
			ps.setInt(1, start);
			ps.setInt(2, sl);
			ResultSet rs = ps.executeQuery();
			while (rs.next()) {
				MSTCUSTOMER mstcustomer = new MSTCUSTOMER() {
				};
				mstcustomer.setCustomer_ID(rs.getString("CUSTOMER_ID"));
				mstcustomer.setCustomer_Name(rs.getString("CUSTOMER_NAME"));
				mstcustomer.setSex(rs.getString("SEX"));
				mstcustomer.setBirthday(rs.getString("BIRTHDAY"));
				mstcustomer.setAddress(rs.getString("ADDRESS"));
				list.add(mstcustomer);
			}
			conn.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return list;

	}

	//ham dem id trong bang
	public int getCount() {
		Connection conn = JdbcSQLServerConnection.getConnection();
		ArrayList<MSTCUSTOMER> list = new ArrayList();
		String sql = "SELECT count(CUSTOMER_ID) FROM MSTCUSTOMER";
		int count = 0;
		try {
			PreparedStatement stmt = conn.prepareStatement(sql);
			ResultSet rs = stmt.executeQuery();
			while (rs.next()) {
				count = rs.getInt(1);
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return count;
	}

	// test

	public static void main(String[] args) {
		MSTCUSTOMER_DAO dao = new MSTCUSTOMER_DAO();

//		 if (dao.DeleteByID("1"))
//		 System.out.println("ok");
//		 else
//		 System.out.println("not ok");

		/*
		 * MSTCUSTOMER cus = new MSTCUSTOMER(); // CUSTOMER_NAME, SEX, ADDRESS,
		 * BIRHDAY, EMAIL, UPDATEYMD, INSERTYMD, DELETEYMD
		 * cus.setS_customerName("bi"); cus.setS_sex("male");
		 * cus.setS_address("quan9"); cus.setS_birthday(new Date(2008, 11, 11));
		 * cus.setS_email("mail"); cus.setD_updateYMD(new Date(2008, 11, 11));
		 * cus.setD_insertYMD(new Date(2008, 11, 11)); cus.setD_deleteYMD(new
		 * Date(2008, 11, 11));
		 * 
		 * if (dao.CreateNew(cus)) System.out.println("ok"); else
		 * System.out.println("not ok");
		 */

		/*
		 * for (MSTCUSTOMER cus : dao.GetCustomerPT(1, 2)) {
		 * System.out.println(cus.toString()); }
		 */

	}

}

package fjs.cs.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;

import fjs.cs.common.JdbcSQLServerConnection;

public class MSTUSER_DAO {
	
	

	public static boolean validate(String username, String password){
		int totalRecord=0;
		try {
			
			//ket noi csdl
			Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			Connection conn=DriverManager.getConnection("jdbc:sqlserver://localhost:1433;" + "databaseName=CustomerSystem","sa", "Abc12345");
		
			String sql="SELECT * FROM MSTUSER WHERE USERID=? AND PASSWORD=?";
			//tao cau lenh lay id va pass
			PreparedStatement ps=conn.prepareStatement(sql);
			
			//truyen tham so
			ps.setString(1,username);  
			ps.setString(2,password); 
			
			//tra ket qua thuc thi cau lenh sql
			ResultSet rs=ps.executeQuery();  
			while(rs.next()){
				totalRecord++;
			}
			conn.close();
			
		} catch (Exception e) {
			System.out.println(e);
		}
		return totalRecord > 0 ? true : false;
	}
	public static void main(String[] args) {
//		try {
//            // connnect to database 'testdb'
//            Connection conn = JdbcSQLServerConnection.getConnection();
//            // crate statement
//            Statement stmt = conn.createStatement();
//            // get data from table 'student'
//            ResultSet rs = stmt.executeQuery("select * from MSTUSER");
//            // show data
//            while (rs.next()) {
//                System.out.println(rs.getInt(1) + "  " + rs.getString(2) 
//                        + "  " + rs.getString(3));
//            }
//            // close connection
//            conn.close();
//        } catch (Exception ex) {
//            ex.printStackTrace();
//        }
    }
}




T001
<%@page
	import="com.sun.org.apache.xml.internal.security.transforms.params.InclusiveNamespaces"%>
<%@page import="com.sun.xml.internal.txw2.Document"%>
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>T001_Login</title>
<script type="text/javascript" src="WebContent/js/error.js"></script>
</head>
<body style="background-color: #66ffff;">
	<div class="container"></div>
	<div class="panel panel-default">
		<div class="panel-heading">
			<h2 style="color: red;">Tranning</h2>
			<hr>
			<p>Login</p>
		</div>
		<div class="panel-body">
			<div class="row">
				<h2 style="text-align: center; color: blue">LOGIN</h2>
				<center>
					<p style="text-align: center">
						<label id="lblErrorMessage"></label>
					</p>
				</center>
			</div>

			<form class="form-control" name="myForm" style="text-align: center"
				action="Login" method="post" onsubmit="return myFunction()">
				<div class="row">
					<div class="form-group">
						<label>User Id:</label> <input
							<%=request.getAttribute("username")%> id="txtUserID"
							name="txtUserID" style="margin-left: 14px" type="text" value="" />
					</div>
					<div class="form-group" style="margin-top: 10px;">
						<label>Password:</label> <input
							<%=request.getAttribute("password")%> id="txtPassword"
							name="txtPassword" type="password" value="" />
					</div>
				</div>

				<div class="row">
					<div class="form-group" style="margin-top: 20px">
						<input action="login" name="action" hidden value="login">
						<input onclick="myFunction()" type="submit" name="btnLogin"
							style="margin-left: 50px" value="Login" /> <input type="submit"
							name="btnClear" onclick="myFunction1()"
							style="margin-left: 100px" value="Clear" />
					</div>
			</form>

		</div>


	</div>



</body>

</html>

