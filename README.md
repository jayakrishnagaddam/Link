# Link

-- USERS TABLE
CREATE TABLE Users (
  user_id INT PRIMARY KEY IDENTITY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  created_at DATETIME DEFAULT GETDATE()
);

INSERT INTO Users (email, password_hash)
VALUES 
('john.doe@example.com', 'hashed_pass_123'),
('jane.smith@example.com', 'hashed_pass_456'),
('alex.lee@example.com', 'hashed_pass_789');

-- USER PROFILE TABLE
CREATE TABLE UserProfile (
  profile_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  name VARCHAR(100),
  age INT,
  gender VARCHAR(10),
  height_cm FLOAT,
  weight_kg FLOAT,
  goal_type VARCHAR(50),
  target_weight FLOAT,
  activity_level VARCHAR(50)
);

INSERT INTO UserProfile (user_id, name, age, gender, height_cm, weight_kg, goal_type, target_weight, activity_level)
VALUES
(1, 'John Doe', 28, 'Male', 175, 82, 'Weight Loss', 75, 'Moderate'),
(2, 'Jane Smith', 24, 'Female', 160, 60, 'Muscle Gain', 65, 'High'),
(3, 'Alex Lee', 32, 'Non-binary', 170, 70, 'Weight Loss', 65, 'Low');

-- WORKOUTS TABLE
CREATE TABLE Workouts (
  workout_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  date DATE,
  workout_type VARCHAR(100),
  duration_minutes INT,
  calories_burned INT
);

INSERT INTO Workouts (user_id, date, workout_type, duration_minutes, calories_burned)
VALUES
(1, '2025-05-05', 'Running', 30, 300),
(2, '2025-05-05', 'Yoga', 45, 180),
(3, '2025-05-06', 'Cycling', 60, 500);

-- STEPS TABLE
CREATE TABLE Steps (
  step_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  date DATE,
  steps_count INT
);

-- MEALS TABLE
CREATE TABLE Meals (
  meal_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  date DATE,
  meal_type VARCHAR(50),
  food_items TEXT,
  total_calories INT
);

-- SLEEP LOGS
CREATE TABLE SleepLogs (
  sleep_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  date DATE,
  sleep_duration_hours FLOAT
);

-- WATER INTAKE
CREATE TABLE WaterIntake (
  intake_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  date DATE,
  glasses_of_water INT
);

-- REMINDERS
CREATE TABLE Reminders (
  reminder_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  reminder_type VARCHAR(50),
  reminder_time TIME
);

-- NOTIFICATIONS
CREATE TABLE Notifications (
  notification_id INT PRIMARY KEY IDENTITY,
  user_id INT FOREIGN KEY REFERENCES Users(user_id),
  message TEXT,
  created_at DATETIME DEFAULT GETDATE(),
  is_read BIT DEFAULT 0
);

-- FRIENDS
CREATE TABLE Friends (
  user_id INT,
  friend_id INT,
  PRIMARY KEY (user_id, friend_id),
  FOREIGN KEY (user_id) REFERENCES Users(user_id),
  FOREIGN KEY (friend_id) REFERENCES Users(user_id)
);

-- CHALLENGES
CREATE TABLE Challenges (
  challenge_id INT PRIMARY KEY IDENTITY,
  title VARCHAR(100),
  description TEXT,
  start_date DATE,
  end_date DATE
);

CREATE TABLE ChallengeParticipants (
  user_id INT,
  challenge_id INT,
  status VARCHAR(50),
  PRIMARY KEY (user_id, challenge_id),
  FOREIGN KEY (user_id) REFERENCES Users(user_id),
  FOREIGN KEY (challenge_id) REFERENCES Challenges(challenge_id)
);

