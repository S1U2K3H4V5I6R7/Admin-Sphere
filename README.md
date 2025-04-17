# Admin-Sphere
Emphasizea Comprehensive Admin DashBoard
1. Core Component ImplementationA


User Registration Module java
// Registration Servlet.java
protectedvoiddoPost(HttpServletRequestrequest,HttpServletResponseresponse){ try {
User user = new User(); user.setFirstName(request.getParameter("firstName"));
user.setPassword(PasswordHasher.hash(request.getParameter("password")));

UserDAO.create(user);//Databaseinsertion response.sendRedirect("success.jsp");
} catch (Exception e) {	request.setAttribute("error", e.getMessage()); request.getRequestDispatcher("error.jsp").forward(request, response);
} }

/ LoginServlet.java
Stringemail = request.getParameter("email");
Stringpassword=request.getParameter("password");

Useruser = UserDAO.findByEmail(email);
if(user != null && PasswordHasher.verify(password, user.getPassword())) { HttpSession session = request.getSession();	session.setAttribute("currentUser",user); response.sendRedirect("dashboard.jsp");
} else {	request.setAttribute("error","Invalid credentials");
request.getRequestDispatcher("login.jsp").forward(request,response);}
C.AdminOTPVerificationjava
// OTPServlet.java
String otp = OTPGenerator.generate(6); // 6-digit numeric OTP Twilio.init(ACCOUNT_SID,AUTH_TOKEN);Message.creator(
newPhoneNumber(adminMobile), new PhoneNumber("+1234567890"),
"YourOTP:"+otp
).create();

// Store in session request.getSession().setAttribute("otp",otp);

3. Database Implementation MySQL Schema sql

CREATE TABLE users      (id INT AUTO_INCREMENT
PRIMARY KEY,first_name
VARCHAR(50)NOTNULL, emailVARCHAR(100) UNIQUE
NOTNULL, password_hashCHAR(64) NOTNULL, --SHA-
256 mobile VARCHAR(15), created_atTIMESTAMPDEFAULT CURRENT_TIMESTAMP
);
CREATE TABLE feedback(id INT AUTO_INCREMENT
response.setHeader("Content-Disposition","attachment;filename=users.xls"); List<User> users = UserDAO.getAllUsers();
HSSFWorkbookworkbook=newHSSFWorkbook();
//Createsheetandpopulatedataworkbook.write(response.getOutputStream());

4. AdminDashboardImplementationUser Management Features java
// UserListServlet.java
List<User> users = UserDAO.getAllUsers(); request.setAttribute("users", users); request.getRequestDispatcher("admin/users.jsp").forward(request, response); JSP Data Display:jsp
Copy
<table class="table">
<c:forEachitems="${users}"var="user">
<tr>
<td>${user.id}</td>
<td>${user.firstName}${user.lastName}</td>
<td>
<ahref="edit-user.jsp?id=${user.id}"class="btnbtn-warning">Edit</a>
<ahref="delete-user?id=${user.id}"
onclick="return confirm('Delete this user?')"	class="btn btn-danger">Delete</a>
</td>
</tr>
</c:forEach>
</table>


5.ExcelExportImplementation java
// ExcelExportServlet.java response.setContentType("application/vnd.ms-excel");
 
response.setHeader("Content-Disposition","attachment;filename=users.xls"); List<User> users = UserDAO.getAllUsers();
HSSFWorkbookworkbook=newHSSFWorkbook();
//Createsheetandpopulatedataworkbook.write(response.getOutputStream());

 SecurityImplementation CriticalSecurity Measures
1.	Input Sanitization java public static String sanitize(String input) {	return input.replaceAll("<",
"&lt;")
.replaceAll(">","&gt;");
}
2.Session Protection java // In web.xml
<session-config>
<session-timeout>30</session-timeout>
<cookie-config>
<http-only>true</http-only>
<secure>true</secure>
</cookie-config>
</session-config>

3.Password Hashing java
publicclassPasswordHasher{	publicstatic
Stringhash(Stringpassword){	Stringsalt
= BCrypt.gensalt();	return BCrypt.hashpw(password,salt);
}
}
 
 Exception Handling ImplementationGlobal Error Handler java

// ErrorHandlerServlet.java protected void doGet(HttpServletRequest request, HttpServletResponse response) {	Throwable exception = (Throwable)request.getAttribute("javax.servlet.error.exception");	logger.error("System error", exception);
request.getRequestDispatcher("/system-error.jsp").forward(request,response);
}Database Error Recovery java try { Connectionconn=Database.getConnection();
// DB operations } catch (SQLException e) {	if(e.getErrorCode() == 1062) { // Duplicate entry	request.setAttribute("error", "Email already exists");		request.getRequestDispatcher("register.jsp").forward(request, response);	} else {
thrownewServletException("Databaseerror",e);
}
}

 PerformanceOptimizationCaching Strategy java
// UserCache.java public class UserCache {	privatestaticfinal Map<Integer, User> cache = new LRUCache<>(100);

publicstaticUserget(intuserId){ if(cache.contains(userId)) {
return cache.get(userId);
}else {
Useruser=UserDAO.getById(userId); cache.put(userId, user);	return user;
}
}
}

 DeploymentConfigurationTomcat Context xml
<!--context.xml-->
<Context>
<Resource name="jdbc/UserDB"		auth="Container" type="javax.sql.DataSource"			maxTotal="100" maxIdle="30"	username="admin" password="encrypted_password" driverClassName="com.mysql.cj.jdbc.Driver" url="jdbc:mysql://localhost:3306/userdb?useSSL=false"/>
</Context>
BuildAutomation xml
<!--pom.xml-->
<build>
<pl

<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-war-plugin</artifactId>
<version>3.3.2</version>
</plugin>
</plugins><
</build>



