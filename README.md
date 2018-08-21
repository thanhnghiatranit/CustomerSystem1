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
