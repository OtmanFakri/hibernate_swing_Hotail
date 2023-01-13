# Swing App + Hibernate + MYSQL


# Tools 
* Using Hibernate Framework
* MySQL Server 





# Usage

* The configuration file can be found in ``` hibernate.cfg.xml ``` in ``` src/resources/```

* All relevant java source code can be found in ```src/main/java```

# SQL Tables 
```
CREATE TABLE hotel_project.Bookings ( ID int NOT NULL AUTO_INCREMENT, TennantId int NOT NULL, roomNumber int NOT NULL, noDaysBooked int NOT NULL, roomStatus varchar(255) NOT NULL, checkIn datetime NOT NULL, checkOut datetime NOT NULL, PRIMARY KEY (ID), FOREIGN KEY (TennantId) REFERENCES Tennants(ID) );
```
```
CREATE TABLE hotel_project.Tennants (ID int NOT NULL AUTO_INCREMENT, Title varchar(255) NOT NULL, FirstName varchar(255) NOT NULL, LastName varchar(255) NOT NULL, NoAdultVisitors varchar(255) NOT NULL, NoMiniorsVisitors varchar(255) NOT NULL, PRIMARY KEY (ID));
```
```
CREATE TABLE hotel_project.Payments ( ID int NOT NULL AUTO_INCREMENT, TennantId int NOT NULL, BookingId int NOT NULL, Total decimal(19,4) NOT NULL, PaymentType varchar(255) NOT NULL, PaymentStatus varchar(255) NOT NULL, Tax decimal(19,4) NOT NULL, DateOfPayment date NOT NULL, PRIMARY KEY (ID), FOREIGN KEY (TennantId) REFERENCES Tennants(ID), FOREIGN KEY (bookingId) REFERENCES Bookings(ID) );

```

# Java object models 
* Tenant.java
```
@Entity
// table name
@Table(name = "Tennants")
public class Tenant {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "ID")
    private int id;

    @Column(name = "Title")
    private String title; // customers initial title

    @Column(name = "FirstName")
    private String firstName; // customers firstname

    @Column(name = "LastName")
    private String lastName; // lastname

    @Column(name = "NoAdultVisitors")
    private String noAdultVisitors; //  No of Adult Visitors

    @Column(name = "NoMiniorsVisitors")
    private String noMiniorsVisitors; // No of Minor Visitors


    public Tenant() {
    };

    // constructor to pass in values
    public Tenant(String title, String firstN, String lastN, String adults,
                  String minors) {

        this.title = title;
        this.firstName = firstN;
        this.lastName = lastN;
        this.noAdultVisitors = adults;
        this.noMiniorsVisitors = minors;
    }


    public int getId() {
        return id;
    }

    // getter method for title
    public String getTitle() {
        return this.title;
    }

    // getter method for firstname
    public String getFirstName() {
        return this.firstName;
    }

    // getter method for lastname
    public String getLastName() {
        return this.lastName;
    }

    // getter method for
    public String getNoAdultVisitors() {
        return this.noAdultVisitors;
    }

    // getter method for No of Minor Visitors
    public String getNoMinorsVisitors() {
        return this.noMiniorsVisitors;
    }


    public void setId(int id) {
        this.id = id;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public void setNoAdultVisitors(String noAdultVisitors) {
        this.noAdultVisitors = noAdultVisitors;
    }

    public void setNoMiniorsVisitors(String noMiniorsVisitors) {
        this.noMiniorsVisitors = noMiniorsVisitors;
    }

    /// string method so its prints the object content
    public String toString() {
        return "> Title:" + this.title + " Name:" + this.firstName + " " +
                this.lastName;
    }
}
```

* Payment.java 
```
@Entity
// table name
@Table(name = "Payments")
public class Payment {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name = "ID")
	private int id;

	@Column(name = "TennantId")
	private int tennantId;

	@Column(name = "BookingId")
	private int bookingId;

	@Column(name = "Total")
	private double total;

	@Column(name = "PaymentType")
	private String paymentType;

	@Column(name = "PaymentStatus")
	private String paymentStatus;

	@Column(name = "Tax")
	private double tax;

	@Column(name = "DateOfPayment")
	private java.time.LocalDateTime dateOfPayment;

	private static final int rateDay = 200; // the rate that apply each day

	private static final double taxRate = 0.10; // tenants have to pay 10% tax


	public Payment(){};

	public Payment(int tennantId, int bookingId, int nod) {
		this.tennantId = tennantId;
		this.bookingId = bookingId;
		this.total = totalCal(nod);
		this.paymentType = "No_Payment";
		this.paymentStatus = "Not_Paid";
		this.tax = taxCal(this.total);
		this.dateOfPayment = this.dateOfPayment.now();
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public int getTennantId() {
		return tennantId;
	}

	public void setTennantId(int tennantId) {
		this.tennantId = tennantId;
	}

	public int getBookingId() {
		return bookingId;
	}

	public void setBookingId(int bookingId) {
		this.bookingId = bookingId;
	}

	public double getTotal() {
		return total;
	}

	public void setTotal(double total) {
		this.total = total;
	}

	public String getPaymentType() {
		return paymentType;
	}

	public void setPaymentType(String paymentType) {
		this.paymentType = paymentType;
	}

	public String getPaymentStatus() {
		return paymentStatus;
	}

	public void setPaymentStatus(String paymentStatus) {
		this.paymentStatus = paymentStatus;
	}

	public double getTax() {
		return tax;
	}

	public void setTax(double tax) {
		this.tax = tax;
	}

	public LocalDateTime getDateOfPayment() {
		return dateOfPayment;
	}

	public void setDateOfPayment(LocalDateTime dateOfPayment) {
		this.dateOfPayment = dateOfPayment;
	}

	// public return method for tax calculation
	public double taxCal(double amount) {
		return amount * taxRate;
	}
	// public return method for payment before
	public double totalCal(int num) {
		return num * rateDay;
	}
}// end of class
```
* Booking.java 
```
@Entity
// table name
@Table(name="Bookings")
public class Booking {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
@Column(name="ID")
private int id;

@Column(name="TennantId")
private int tennantsId;

// securing all global variables as private
@Column(name="roomNumber")
private int roomNumber; // visitors room number

@Column(name="roomStatus")
private String roomStatus; // status if the room is available or not

@Column(name="noDaysBooked")
private int noDaysBooked; // visitors room number

@Column(name="checkIn")
private java.time.LocalDateTime checkIn;

@Column(name="checkOut")
private java.time.LocalDateTime checkOut;


public Booking(){};

// constructor to create a room object
// inside the constructor you pass in room number, visitor name and boolean value if the room
// is available or not 
public Booking(int tennantsId, int rn, String _checkout ){
	this.roomNumber = rn;
    this.tennantsId = tennantsId;
	this.checkIn = this.checkIn.now();
	this.checkOut = this.checkOut.parse(_checkout+" 00:00:00.0".replace( " " , "T" ));
	this.roomStatus = "Not Available";
	this.noDaysBooked = this.calculateNoDaysBooked();
	}


	public int getId() {
		return id;
	}

	public int getTennantsId() {
		return tennantsId;
	}

	public int getRoomNumber() {
		return roomNumber;
	}

	public String getRoomStatus() {
		return roomStatus;
	}

	public int calculateNoDaysBooked() {

	return  (int)ChronoUnit.DAYS.between(this.checkIn, this.checkOut);

	}

	public int getNoDaysBooked() {
		return noDaysBooked;
	}

	public LocalDateTime getCheckIn() {
		return checkIn;
	}

	public LocalDateTime getCheckOut() {
		return checkOut;
	}
}
```


   
