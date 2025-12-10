# 🏥 Hospital Management System – Stored Procedures

## 📘 Overview

This repository contains a set of MySQL stored procedures developed as part of an academic assignment for a hospital management system.
The goal was to design and implement procedures that retrieve meaningful insights from a relational database consisting of Patients, Doctors,
and Appointments tables.

## 🎯 Objective

To demonstrate proficiency in SQL by:
• 	Writing stored procedures using JOINs across multiple tables
• 	Extracting relevant appointment and financial data
• 	Performing aggregate calculations and filtering based on input parameters

## 📌 How to Use

- Clone the repository.
- Import the SQL schema into your MySQL database.
- Execute the stored procedures using CALL statements like:

	 	 CALL GetPatientAppointments(1);
		CALL GetAppointmentsInRange('2024-03-01', '2024-03-31');
