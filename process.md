### **Apex MD Booking Widget: Implementation Summary**  

**What We'll Deliver:**  
A **branded scheduling widget** with your logo & red accents that:  
1. Shows real-time availability via Healthie API  
2. Books appointments in 4 steps (Type → Date → Time → Info)  
3. Works standalone or embedded on apexmd.com  
4. Matches [your reference design](https://express.patientcare.openloophealth.com/book-appointment?appointmentTypeId=458325,458326&providerId=8093900)  

**Tech & Method:**  
- **Frontend**: Pure HTML/CSS/JS (no frameworks) for easy embedding  
- **Backend**: Direct integration with Healthie's GraphQL API  
- **Branding**: Your logo + custom red accents (#e63946 proposed)  
- **Testing**: Rigorous checks with test card `4242 4242 4242 4242`  

**Final Deliverables:**  
1. `booking-widget.html` file (standalone)  
2. Embed code for your website:  
   ```html 
   <iframe src="https://yourdomain.com/booking-widget.html" width="100%" height="650px"></iframe>
   ```  
3. Documentation for maintenance  
4. 30-day bug-free support  


**Required from You:**  
1. **Logo file** (PNG/PSD)  
2. **Exact red color code** (or approve `#e63946`)  
3. **Confirmation**:  
   ```js
   API Endpoint: https://api.gethealthie.com/graphql  
   Provider ID: 8093900  // ✔️ Verify this matches your Healthie account
   ```  
