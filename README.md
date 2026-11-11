# Hospital Appointment Booking System
BE CSE Mini Project - Spring Boot + MySQL + HTML/CSS/JavaScript

## Technologies used

| Layer      | Technology                                             |
|------------|--------------------------------------------------------|
| Frontend   | HTML5, CSS3, JavaScript (Fetch API, no framework)      |
| Backend    | Java 17, Spring Boot 3.3 (Spring Web, REST controllers)|
| ORM        | Spring Data JPA + Hibernate                            |
| Database   | MySQL 8 (H2 in-memory optional)                        |
| Security   | BCrypt password hashing (spring-security-crypto)       |
| Build tool | Maven                                                  |
| IDE        | Eclipse                                                |

## Features
- Patient: register, login, view doctors, pick a date, see free / booked time slots, book, cancel, track status
- Admin: view all appointments, Confirm / Complete / Cancel, add and delete doctors, dashboard counts
- Double booking is blocked (same doctor + date + time)
- Past dates and past time slots cannot be booked
- Passwords are stored as BCrypt hashes

## How to run in Eclipse

### Step 1 - Install requirements
1. JDK 17 or higher
2. MySQL Server 8 (keep it running)
3. Eclipse IDE for Enterprise Java (any recent version; Maven support is built in)

### Step 2 - Import the project
1. Extract the zip file.
2. Eclipse -> **File -> Import -> Maven -> Existing Maven Projects** -> Next
3. Browse to the extracted `hospital-appointment-system` folder -> Finish
4. Wait until the Maven dependencies finish downloading (progress bar at bottom right). Internet is needed for this step.

### Step 3 - Set your MySQL username and password
Open `src/main/resources/application.properties` and change these two lines:

```
spring.datasource.username=root
spring.datasource.password=root
```

You do NOT need to create the database or any table by hand:
- the database `hospital_db` is created automatically (`createDatabaseIfNotExist=true`)
- the tables `users`, `doctors`, `appointments` are created automatically (`spring.jpa.hibernate.ddl-auto=update`)
- an admin user and 6 sample doctors are inserted automatically on first run

### Step 4 - Run
Right click `HospitalAppointmentApplication.java` -> **Run As -> Java Application**
(or **Spring Boot App** if you have Spring Tools installed).

When the console shows `Started HospitalAppointmentApplication`, open:

**http://localhost:8080**

### Logins
| Role    | Username | Password |
|---------|----------|----------|
| Admin   | admin    | admin123 |
| Patient | click **Register** and create your own |

## No MySQL installed? (quick demo mode)
In `application.properties` remove the `#` before `spring.profiles.active=h2`.
The app then uses an in-memory database (data is lost when you stop the app).

## Project structure
```
src/main/java/com/hospital/appointment
  controller/   AuthController, DoctorController, AppointmentController   (REST APIs)
  model/        User, Doctor, Appointment                                 (database tables)
  repository/   UserRepository, DoctorRepository, AppointmentRepository   (Spring Data JPA)
  service/      AccessService                                             (login / role check)
  dto/          request and response objects
  config/       AppConfig (BCrypt), DataSeeder (default data)
src/main/resources
  application.properties                                                  (DB username/password)
  static/       index.html, login.html, register.html, patient.html, admin.html
                css/style.css   js/app.js, auth.js, patient.js, admin.js
```

## REST API summary
| Method | URL                               | Who      | Purpose                  |
|--------|-----------------------------------|----------|--------------------------|
| POST   | /api/auth/register                | public   | register patient         |
| POST   | /api/auth/login                   | public   | login                    |
| GET    | /api/doctors                      | any      | list doctors             |
| GET    | /api/doctors/{id}/slots?date=     | any      | free / booked slots      |
| POST   | /api/doctors                      | admin    | add doctor               |
| DELETE | /api/doctors/{id}                 | admin    | delete doctor            |
| POST   | /api/appointments                 | patient  | book appointment         |
| GET    | /api/appointments/my              | patient  | my appointments          |
| PUT    | /api/appointments/{id}/cancel     | owner/admin | cancel                |
| GET    | /api/appointments                 | admin    | all appointments         |
| PUT    | /api/appointments/{id}/status     | admin    | change status            |

## Database tables (auto created)
- `users` (id, full_name, username, password, email, phone, role)
- `doctors` (id, name, specialization, qualification, experience_years, consultation_fee)
- `appointments` (id, patient_id -> users, doctor_id -> doctors, appointment_date, time_slot, reason, status, created_at)

## Common problems
- **Access denied for user 'root'** -> wrong MySQL password in `application.properties`.
- **Port 8080 already in use** -> change `server.port=8081` in `application.properties`.
- **Red errors after import** -> right click project -> Maven -> Update Project (Alt+F5), tick Force Update.
- **Java version error** -> Window -> Preferences -> Java -> Installed JREs: select JDK 17 or higher.
