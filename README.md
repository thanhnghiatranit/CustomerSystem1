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
