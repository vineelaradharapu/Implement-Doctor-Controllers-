# Implement-Doctor-Controllers-
Create controllers to handle doctor-related logic, including listing doctors, fetching doctor details, and booking appointments.
// controllers/doctorController.js
const Doctor = require('../models/Doctor');

// Get a list of doctors
exports.getDoctors = async (req, res) => {
  try {
    const doctors = await Doctor.find();
    res.status(200).json(doctors);
  } catch (error) {
    res.status(500).json({ error: 'Internal server error' });
  }
};

// Get doctor details by ID
exports.getDoctorById = async (req, res) => {
  try {
    const doctor = await Doctor.findById(req.params.id);
    if (!doctor) {
      return res.status(404).json({ error: 'Doctor not found' });
    }
    res.status(200).json(doctor);
  } catch (error) {
    res.status(500).json({ error: 'Internal server error' });
  }
};

// Book an appointment with a doctor
exports.bookAppointment = async (req, res) => {
  try {
    const doctor = await Doctor.findById(req.params.id);
    if (!doctor) {
      return res.status(404).json({ error: 'Doctor not found' });
    }

    // Check if there are available appointments for the selected day
    const selectedDay = req.body.dayOfWeek;
    const selectedSchedule = doctor.schedule.find(
      (schedule) => schedule.dayOfWeek === selectedDay
    );

    if (!selectedSchedule) {
      return res.status(400).json({ error: 'Invalid day selected' });
    }

    if (selectedSchedule.maxAppointments <= 0) {
      return res.status(400).json({ error: 'No available appointments for this day' });
    }

    // Decrement the available appointments count
    selectedSchedule.maxAppointments--;

    // Save the updated doctor's schedule
    await doctor.save();

    // Optionally, you can save the booking details in your database

    res.status(201).json({ message: 'Appointment booked successfully' });
  } catch (error) {
    res.status(500).json({ error: 'Internal server error' });
  }
};

