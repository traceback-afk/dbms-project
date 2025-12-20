```sql
CREATE TABLE Train (
    train_id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    type VARCHAR(20),
    total_coaches INT,
    year_built SMALLINT,
    last_maintained DATE,
    status VARCHAR(20) DEFAULT 'On Time' -- On Time / Delayed / Maintenance
);

CREATE TABLE Coach (
    coach_id INT PRIMARY KEY,
    train_id INT NOT NULL,
    type VARCHAR(20), -- Sleeper / AC / General
    capacity INT,
    FOREIGN KEY (train_id) REFERENCES Train(train_id)
);

CREATE TABLE Seat (
    seat_id INT PRIMARY KEY,
    coach_id INT NOT NULL,
    seat_no VARCHAR(5),
    class VARCHAR(20),
    is_booked BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (coach_id) REFERENCES Coach(coach_id)
);

CREATE TABLE Station (
    station_id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    city VARCHAR(50),
    state VARCHAR(50),
    latitude DECIMAL(9,6),
    longitude DECIMAL(9,6),
    platforms INT
);

CREATE TABLE Route (
    route_id INT PRIMARY KEY,
    train_id INT NOT NULL,
    origin_station_id INT NOT NULL,
    destination_station_id INT NOT NULL,
    FOREIGN KEY (train_id) REFERENCES Train(train_id),
    FOREIGN KEY (origin_station_id) REFERENCES Station(station_id),
    FOREIGN KEY (destination_station_id) REFERENCES Station(station_id)
);

CREATE TABLE RouteStation (
    route_station_id INT PRIMARY KEY,
    route_id INT NOT NULL,
    station_id INT NOT NULL,
    sequence_number INT NOT NULL,
    distance_from_origin DECIMAL(6,2) NOT NULL,
    FOREIGN KEY (route_id) REFERENCES Route(route_id),
    FOREIGN KEY (station_id) REFERENCES Station(station_id)
);

CREATE TABLE Schedule (
    schedule_id INT PRIMARY KEY,
    train_id INT NOT NULL,
    route_station_id INT NOT NULL,
    arrival_time TIME,
    departure_time TIME,
    FOREIGN KEY (train_id) REFERENCES Train(train_id),
    FOREIGN KEY (route_station_id) REFERENCES RouteStation(route_station_id)
);

CREATE TABLE Passenger (
    passenger_id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    date_of_birth DATE,
    phone VARCHAR(15),
    email VARCHAR(50)
);

CREATE TABLE Booking (
    booking_id INT PRIMARY KEY,
    pnr VARCHAR(10) UNIQUE,
    train_id INT NOT NULL,
    passenger_id INT NOT NULL,
    booking_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    travel_date DATE,
    from_station_id INT NOT NULL,
    to_station_id INT NOT NULL,
    status VARCHAR(20) DEFAULT 'Confirmed', -- Confirmed / Cancelled
    FOREIGN KEY (train_id) REFERENCES Train(train_id),
    FOREIGN KEY (passenger_id) REFERENCES Passenger(passenger_id),
    FOREIGN KEY (from_station_id) REFERENCES Station(station_id),
    FOREIGN KEY (to_station_id) REFERENCES Station(station_id),
);

CREATE TABLE BookingSeat (
    booking_seat_id INT PRIMARY KEY,
    seat_id INT NOT NULL,
    booking_id INT NOT NULL,
    FOREIGN KEY (booking_id) REFERENCES Booking(booking_id) ON DELETE CASCADE,
    FOREIGN KEY (seat_id) REFERENCES Seat(seat_id) ON DELETE CASCADE
);

CREATE TABLE Driver (
    driver_id INT PRIMARY KEY,
    name VARCHAR(50),
    phone VARCHAR(15),
    license_number VARCHAR(20),
    experience_years INT,
    salary DECIMAL(10,2)
);

CREATE TABLE TrainDriver (
    train_driver_id INT PRIMARY KEY,
    train_id INT,
    driver_id INT,
    FOREIGN KEY (train_id) REFERENCES Train(train_id),
    FOREIGN KEY (driver_id) REFERENCES Driver(driver_id)
);

CREATE TABLE Payment (
    payment_id INT PRIMARY KEY,
    booking_id INT NOT NULL,
    amount DECIMAL(10,2),
    method VARCHAR(20), -- Card / Cash / Online
    status VARCHAR(20) DEFAULT 'Paid',
    FOREIGN KEY (booking_id) REFERENCES Booking(booking_id)
);

CREATE TABLE Maintenance (
    maintenance_id INT PRIMARY KEY,
    train_id INT NOT NULL,
    maintenance_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    type VARCHAR(50),
    technician_name VARCHAR(50),
    FOREIGN KEY (train_id) REFERENCES Train(train_id)
);

CREATE TABLE IncidentLog (
    incident_id INT PRIMARY KEY,
    train_id INT,
    incident_time DATETIME,
    description TEXT,
    latitude DECIMAL(9,6),
    longitude DECIMAL(9,6),
    FOREIGN KEY (train_id) REFERENCES Train(train_id),
);

```
