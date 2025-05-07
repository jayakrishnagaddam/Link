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

-- SQL schema for Fitness App

-- Users table (general user info including clients, trainers, and admins)
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    password_hash VARCHAR(255),
    role ENUM('client', 'trainer', 'admin') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO Users (name, email, password_hash, role) VALUES
('Alice Smith', 'alice@example.com', 'hashed_pw1', 'client'),
('Bob Trainer', 'bob@example.com', 'hashed_pw2', 'trainer'),
('Carol Admin', 'carol@example.com', 'hashed_pw3', 'admin');

-- Client profiles with fitness goals and stats
CREATE TABLE ClientProfiles (
    client_id INT PRIMARY KEY,
    age INT,
    height_cm INT,
    weight_kg INT,
    gender ENUM('male', 'female', 'other'),
    fitness_goal VARCHAR(100),
    fitness_level ENUM('beginner', 'intermediate', 'advanced'),
    FOREIGN KEY (client_id) REFERENCES Users(user_id)
);

INSERT INTO ClientProfiles (client_id, age, height_cm, weight_kg, gender, fitness_goal, fitness_level) VALUES
(1, 28, 165, 60, 'female', 'Weight loss', 'beginner');

-- Workout Programs
CREATE TABLE WorkoutPrograms (
    program_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100),
    category VARCHAR(50),
    duration_minutes INT,
    difficulty ENUM('beginner', 'intermediate', 'advanced'),
    equipment_required TEXT,
    description TEXT,
    popularity_score INT DEFAULT 0
);

INSERT INTO WorkoutPrograms (title, category, duration_minutes, difficulty, equipment_required, description) VALUES
('Full Body Blast', 'Strength', 45, 'intermediate', 'Dumbbells', 'A full body workout to build strength'),
('Morning Yoga', 'Flexibility', 30, 'beginner', 'Yoga Mat', 'Start your day with relaxing yoga');

-- Favorites
CREATE TABLE Favorites (
    user_id INT,
    program_id INT,
    PRIMARY KEY (user_id, program_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (program_id) REFERENCES WorkoutPrograms(program_id)
);

INSERT INTO Favorites (user_id, program_id) VALUES
(1, 1), (1, 2);

-- Workout Tracking
CREATE TABLE WorkoutLogs (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    program_id INT,
    exercise_name VARCHAR(100),
    sets INT,
    reps INT,
    weight_kg DECIMAL(5,2),
    duration_minutes INT,
    log_date DATE,
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (program_id) REFERENCES WorkoutPrograms(program_id)
);

INSERT INTO WorkoutLogs (user_id, program_id, exercise_name, sets, reps, weight_kg, duration_minutes, log_date) VALUES
(1, 1, 'Squat', 3, 12, 20.0, 45, '2025-05-06');

-- Calendar Scheduling
CREATE TABLE WorkoutSchedule (
    schedule_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    program_id INT,
    scheduled_time DATETIME,
    recurrence_pattern VARCHAR(50),
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (program_id) REFERENCES WorkoutPrograms(program_id)
);

INSERT INTO WorkoutSchedule (user_id, program_id, scheduled_time, recurrence_pattern) VALUES
(1, 1, '2025-05-08 07:00:00', 'daily');

-- Reminders
CREATE TABLE Reminders (
    reminder_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    schedule_id INT,
    type ENUM('email', 'push', 'sms'),
    reminder_time INTERVAL,
    reminder_sent BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (schedule_id) REFERENCES WorkoutSchedule(schedule_id)
);

-- Nutrition Logging
CREATE TABLE NutritionLogs (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    log_date DATE,
    meal_name VARCHAR(100),
    calories INT,
    protein_grams DECIMAL(5,2),
    carbs_grams DECIMAL(5,2),
    fats_grams DECIMAL(5,2),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

INSERT INTO NutritionLogs (user_id, log_date, meal_name, calories, protein_grams, carbs_grams, fats_grams) VALUES
(1, '2025-05-06', 'Breakfast - Oats', 300, 10.5, 45.0, 8.0);

-- Trainers table
CREATE TABLE TrainerProfiles (
    trainer_id INT PRIMARY KEY,
    specialization TEXT,
    experience_years INT,
    rating DECIMAL(3,2),
    credentials TEXT,
    FOREIGN KEY (trainer_id) REFERENCES Users(user_id)
);

INSERT INTO TrainerProfiles (trainer_id, specialization, experience_years, rating, credentials) VALUES
(2, 'Strength Training', 5, 4.8, 'ACE Certified');

-- Trainer Reviews
CREATE TABLE TrainerReviews (
    review_id INT PRIMARY KEY AUTO_INCREMENT,
    trainer_id INT,
    client_id INT,
    rating INT,
    comment TEXT,
    review_date DATE,
    FOREIGN KEY (trainer_id) REFERENCES Users(user_id),
    FOREIGN KEY (client_id) REFERENCES Users(user_id)
);

INSERT INTO TrainerReviews (trainer_id, client_id, rating, comment, review_date) VALUES
(2, 1, 5, 'Great trainer and very motivating!', '2025-05-06');

-- Admin Support Tickets
CREATE TABLE SupportTickets (
    ticket_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    category VARCHAR(100),
    status ENUM('open', 'closed', 'in-progress'),
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

INSERT INTO SupportTickets (user_id, category, status, description) VALUES
(1, 'App Bug', 'open', 'Unable to log workout from dashboard');
.....................






-- SQL schema for Fitness App

-- Users table (general user info including clients, trainers, and admins)
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    password_hash VARCHAR(255),
    role ENUM('client', 'trainer', 'admin') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO Users (name, email, password_hash, role) VALUES
('Alice Smith', 'alice@example.com', 'hashed_pw1', 'client'),
('Bob Trainer', 'bob@example.com', 'hashed_pw2', 'trainer'),
('Carol Admin', 'carol@example.com', 'hashed_pw3', 'admin');

-- Client profiles with fitness goals and stats
CREATE TABLE ClientProfiles (
    client_id INT PRIMARY KEY,
    age INT,
    height_cm INT,
    weight_kg INT,
    gender ENUM('male', 'female', 'other'),
    fitness_goal VARCHAR(100),
    fitness_level ENUM('beginner', 'intermediate', 'advanced'),
    FOREIGN KEY (client_id) REFERENCES Users(user_id)
);

INSERT INTO ClientProfiles (client_id, age, height_cm, weight_kg, gender, fitness_goal, fitness_level) VALUES
(1, 28, 165, 60, 'female', 'Weight loss', 'beginner');

-- Workout Programs
CREATE TABLE WorkoutPrograms (
    program_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100),
    category VARCHAR(50),
    duration_minutes INT,
    difficulty ENUM('beginner', 'intermediate', 'advanced'),
    equipment_required TEXT,
    description TEXT,
    popularity_score INT DEFAULT 0
);

INSERT INTO WorkoutPrograms (title, category, duration_minutes, difficulty, equipment_required, description) VALUES
('Full Body Blast', 'Strength', 45, 'intermediate', 'Dumbbells', 'A full body workout to build strength'),
('Morning Yoga', 'Flexibility', 30, 'beginner', 'Yoga Mat', 'Start your day with relaxing yoga');

-- Favorites
CREATE TABLE Favorites (
    user_id INT,
    program_id INT,
    PRIMARY KEY (user_id, program_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (program_id) REFERENCES WorkoutPrograms(program_id)
);

INSERT INTO Favorites (user_id, program_id) VALUES
(1, 1), (1, 2);

-- Workout Tracking
CREATE TABLE WorkoutLogs (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    program_id INT,
    exercise_name VARCHAR(100),
    sets INT,
    reps INT,
    weight_kg DECIMAL(5,2),
    duration_minutes INT,
    log_date DATE,
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (program_id) REFERENCES WorkoutPrograms(program_id)
);

INSERT INTO WorkoutLogs (user_id, program_id, exercise_name, sets, reps, weight_kg, duration_minutes, log_date) VALUES
(1, 1, 'Squat', 3, 12, 20.0, 45, '2025-05-06');

-- Calendar Scheduling
CREATE TABLE WorkoutSchedule (
    schedule_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    program_id INT,
    scheduled_time DATETIME,
    recurrence_pattern VARCHAR(50),
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (program_id) REFERENCES WorkoutPrograms(program_id)
);

INSERT INTO WorkoutSchedule (user_id, program_id, scheduled_time, recurrence_pattern) VALUES
(1, 1, '2025-05-08 07:00:00', 'daily');

-- Reminders
CREATE TABLE Reminders (
    reminder_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    schedule_id INT,
    type ENUM('email', 'push', 'sms'),
    reminder_time INTERVAL,
    reminder_sent BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (schedule_id) REFERENCES WorkoutSchedule(schedule_id)
);

-- Nutrition Logging
CREATE TABLE NutritionLogs (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    log_date DATE,
    meal_name VARCHAR(100),
    calories INT,
    protein_grams DECIMAL(5,2),
    carbs_grams DECIMAL(5,2),
    fats_grams DECIMAL(5,2),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

INSERT INTO NutritionLogs (user_id, log_date, meal_name, calories, protein_grams, carbs_grams, fats_grams) VALUES
(1, '2025-05-06', 'Breakfast - Oats', 300, 10.5, 45.0, 8.0);

-- Trainers table
CREATE TABLE TrainerProfiles (
    trainer_id INT PRIMARY KEY,
    specialization TEXT,
    experience_years INT,
    rating DECIMAL(3,2),
    credentials TEXT,
    FOREIGN KEY (trainer_id) REFERENCES Users(user_id)
);

INSERT INTO TrainerProfiles (trainer_id, specialization, experience_years, rating, credentials) VALUES
(2, 'Strength Training', 5, 4.8, 'ACE Certified');

-- Trainer Reviews
CREATE TABLE TrainerReviews (
    review_id INT PRIMARY KEY AUTO_INCREMENT,
    trainer_id INT,
    client_id INT,
    rating INT,
    comment TEXT,
    review_date DATE,
    FOREIGN KEY (trainer_id) REFERENCES Users(user_id),
    FOREIGN KEY (client_id) REFERENCES Users(user_id)
);

INSERT INTO TrainerReviews (trainer_id, client_id, rating, comment, review_date) VALUES
(2, 1, 5, 'Great trainer and very motivating!', '2025-05-06');

-- Admin Support Tickets
CREATE TABLE SupportTickets (
    ticket_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    category VARCHAR(100),
    status ENUM('open', 'closed', 'in-progress'),
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

INSERT INTO SupportTickets (user_id, category, status, description) VALUES
(1, 'App Bug', 'open', 'Unable to log workout from dashboard');
