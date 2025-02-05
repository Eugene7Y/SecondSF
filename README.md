<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Technician Daily Report (Wizard Style)</title>
  <style>
    body {
      font-family: Arial, sans-serif; 
      margin: 20px; 
      max-width: 600px;
    }
    h2, h3 {
      margin-bottom: 10px;
    }
    fieldset {
      border: 1px solid #ccc;
      padding: 20px;
      margin-bottom: 20px;
      display: none; /* all steps hidden by default */
    }
    fieldset.active {
      display: block; /* show active step */
    }
    label {
      font-weight: bold;
      display: block;
      margin-top: 10px;
    }
    select, input, textarea {
      width: 100%;
      padding: 8px;
      margin-top: 5px;
      box-sizing: border-box;
    }
    .buttons {
      margin-top: 20px;
      display: flex;
      gap: 10px;
    }
    button {
      padding: 10px 15px;
      background-color: #4CAF50;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background-color: #45a049;
    }
    .edit-btn {
      background-color: #ff9800;
    }
    .edit-btn:hover {
      background-color: #e68a00;
    }
    .hidden {
      display: none !important;
    }
  </style>
</head>
<body>

  <h2>Technician Daily Report</h2>
  
  <form id="wizardForm">
    <!-- STEP 1: Assigned Jobs -->
    <fieldset id="step-1" class="active">
      <h3>Step 1: Jobs Assigned</h3>
      <label for="assignedJobs">How many jobs assigned this morning? (0-10)</label>
      <select id="assignedJobs" name="assignedJobs">
        <!-- 0 to 10 -->
        <option value="0">0</option>
        <option value="1">1</option> 
        <option value="2">2</option> 
        <option value="3">3</option> 
        <option value="4">4</option> 
        <option value="5">5</option>
        <option value="6">6</option> 
        <option value="7">7</option> 
        <option value="8">8</option> 
        <option value="9">9</option> 
        <option value="10">10</option> 
      </select>
      
      <div id="assignedJobsComments"></div>

      <div class="buttons">
        <!-- No previous in step 1 -->
        <button type="button" id="step1Next">Next</button>
        <button type="button" class="edit-btn hidden" id="step1Edit">Edit</button>
      </div>
    </fieldset>

    <!-- STEP 2: Completed Jobs -->
    <fieldset id="step-2">
      <h3>Step 2: Jobs Completed</h3>
      <label for="completedJobs">How many jobs completed? (0-10)</label>
      <select id="completedJobs" name="completedJobs">
        <option value="0">0</option>
        <option value="1">1</option> 
        <option value="2">2</option> 
        <option value="3">3</option> 
        <option value="4">4</option> 
        <option value="5">5</option>
        <option value="6">6</option> 
        <option value="7">7</option> 
        <option value="8">8</option> 
        <option value="9">9</option> 
        <option value="10">10</option>
      </select>

      <div id="completedJobsComments"></div>
      
      <div class="buttons">
        <button type="button" id="step2Prev">Previous</button>
        <button type="button" id="step2Next">Next</button>
        <button type="button" class="edit-btn hidden" id="step2Edit">Edit</button>
      </div>
    </fieldset>

    <!-- STEP 3: Rescheduled & Canceled -->
    <fieldset id="step-3">
      <h3>Step 3: Rescheduled & Canceled Jobs</h3>
      <label for="rescheduledJobs">How many jobs rescheduled? (0-10)</label>
      <select id="rescheduledJobs" name="rescheduledJobs">
        <option value="0">0</option>
        <option value="1">1</option> 
        <option value="2">2</option> 
        <option value="3">3</option> 
        <option value="4">4</option> 
        <option value="5">5</option>
        <option value="6">6</option> 
        <option value="7">7</option> 
        <option value="8">8</option> 
        <option value="9">9</option> 
        <option value="10">10</option>
      </select>
      <div id="rescheduledJobsComments"></div>
      
      <label for="canceledJobs">How many jobs canceled? (0-10)</label>
      <select id="canceledJobs" name="canceledJobs">
        <option value="0">0</option>
        <option value="1">1</option> 
        <option value="2">2</option> 
        <option value="3">3</option> 
        <option value="4">4</option> 
        <option value="5">5</option>
        <option value="6">6</option> 
        <option value="7">7</option> 
        <option value="8">8</option> 
        <option value="9">9</option> 
        <option value="10">10</option>
      </select>
      <div id="canceledJobsComments"></div>

      <div class="buttons">
        <button type="button" id="step3Prev">Previous</button>
        <button type="button" id="step3Next">Next</button>
        <button type="button" class="edit-btn hidden" id="step3Edit">Edit</button>
      </div>
    </fieldset>

    <!-- STEP 4: Time and Additional Work -->
    <fieldset id="step-4">
      <h3>Step 4: Extra Work & Timing</h3>
      <label>Were there any additional work requests? What time and what was the response?</label>
      <textarea id="extraWork" placeholder="Describe details..." rows="3"></textarea>

      <label for="firstJobStart">What time did the first job start?</label>
      <input type="time" id="firstJobStart">

      <label for="firstJobArrival">What time did you arrive at the first job?</label>
      <input type="time" id="firstJobArrival">

      <label for="lastJobStart">What time did the last job start?</label>
      <input type="time" id="lastJobStart">

      <label for="lastJobArrival">What time did you arrive at the last job?</label>
      <input type="time" id="lastJobArrival">

      <div class="buttons">
        <button type="button" id="step4Prev">Previous</button>
        <button type="button" id="finalSubmit">Submit Form</button>
        <button type="button" class="edit-btn hidden" id="step4Edit">Edit</button>
      </div>
    </fieldset>

  </form>

  <script>
    /************************************************************************
    * 1) MULTI-STEP WIZARD LOGIC
    *************************************************************************/
    const steps = document.querySelectorAll("fieldset");
    let currentStep = 0; // index of the current step

    // Show the first step (by default, step 0)
    showStep(currentStep);

    function showStep(n) {
      steps.forEach((step, index) => {
        step.classList.remove("active");
        if (index === n) {
          step.classList.add("active");
        }
      });
      // Manage the Edit button visibility
      manageEditButton(n, false); // hidden by default
    }

    // Next/Prev button handlers
    document.getElementById("step1Next").addEventListener("click", () => {
      lockFields(1);
      currentStep = 1;
      showStep(currentStep);
    });
    document.getElementById("step2Next").addEventListener("click", () => {
      lockFields(2);
      currentStep = 2;
      showStep(currentStep);
    });
    document.getElementById("step2Prev").addEventListener("click", () => {
      currentStep = 0;
      showStep(currentStep);
    });
    document.getElementById("step3Next").addEventListener("click", () => {
      lockFields(3);
      currentStep = 3;
      showStep(currentStep);
    });
    document.getElementById("step3Prev").addEventListener("click", () => {
      currentStep = 1;
      showStep(currentStep);
    });
    document.getElementById("step4Prev").addEventListener("click", () => {
      currentStep = 2;
      showStep(currentStep);
    });

    // Lock fields in the specified step index
    function lockFields(stepIndex) {
      // Mark them read-only
      const step = steps[stepIndex - 1]; 
      const inputs = step.querySelectorAll("input, select, textarea");
      inputs.forEach(el => {
        el.readOnly = true; 
        if (el.tagName === "SELECT") el.disabled = true;
      });
      // Show the "Edit" button on that step
      const editBtn = step.querySelector(".edit-btn");
      if (editBtn) {
        editBtn.classList.remove("hidden");
      }
    }

    // Manage Edit button
    function manageEditButton(stepIndex, show) {
      const step = steps[stepIndex];
      const editBtn = step.querySelector(".edit-btn");
      if (editBtn) {
        if (show) editBtn.classList.remove("hidden");
        else editBtn.classList.add("hidden");
      }
    }

    // Attach "Edit" button logic to each step
    ["step1Edit", "step2Edit", "step3Edit", "step4Edit"].forEach(id => {
      const btn = document.getElementById(id);
      if (btn) {
        btn.addEventListener("click", () => {
          // This button toggles read-only fields in the step
          const fieldset = btn.closest("fieldset");
          const inputs = fieldset.querySelectorAll("input, select, textarea");
          let isLocked = inputs[0]?.readOnly || inputs[0]?.disabled; // check first input
          // Toggle
          inputs.forEach(el => {
            if (el.tagName === "SELECT") {
              el.disabled = !el.disabled;
            } else {
              el.readOnly = !el.readOnly;
            }
          });
        });
      }
    });

    /************************************************************************
    * 2) DYNAMIC COMMENT FIELDS FOR JOBS
    *************************************************************************/
    function createCommentFieldsForJobs(selectId, containerId, prefix) {
      const selectEl = document.getElementById(selectId);
      const container = document.getElementById(containerId);

      // On change, regenerate
      selectEl.addEventListener("change", () => {
        generateCommentFields();
      });

      function generateCommentFields() {
        container.innerHTML = ""; // clear
        const count = parseInt(selectEl.value);
        for (let i = 1; i <= count; i++) {
          const label = document.createElement("label");
          label.textContent = `${prefix} Job #${i} Comment:`;
          const textarea = document.createElement("textarea");
          textarea.name = `${selectId}Comment${i}`;
          textarea.placeholder = `Comment for job #${i}`;
          textarea.rows = 2;
          container.appendChild(label);
          container.appendChild(textarea);
        }
      }

      // Initialize once
      generateCommentFields();
    }

    createCommentFieldsForJobs("assignedJobs", "assignedJobsComments", "Assigned");
    createCommentFieldsForJobs("completedJobs", "completedJobsComments", "Completed");
    createCommentFieldsForJobs("rescheduledJobs", "rescheduledJobsComments", "Rescheduled");
    createCommentFieldsForJobs("canceledJobs",   "canceledJobsComments",   "Canceled");

    /************************************************************************
    * 3) FINAL SUBMIT LOGIC
    *************************************************************************/
    const finalSubmitBtn = document.getElementById("finalSubmit");
    finalSubmitBtn.addEventListener("click", () => {
      // Lock fields in step 4
      lockFields(4);

      // Prompt for "Where to send?"
      const destination = prompt("Where would you like to send this report?\n\n(For example: Telegram group name, or 'Save on phone')", "Save on phone");

      // If user canceled or typed nothing, do nothing
      if (!destination) {
        alert("No destination selected. Form data is kept in this page only.");
        return;
      }

      // Gather data from all steps
      const formData = gatherFormData();

      // For demonstration, just show the result and where we’ll send
      alert(`Report is ready to be sent to: ${destination}\n\nOpen the console to view full data.`);
      console.log("Final Form Data:", formData);

      // If you want to store locally (on phone):
      // localStorage.setItem("dailyReport", JSON.stringify(formData));

      // If you want to actually send to Telegram or somewhere else,
      // you would call your API or Telegram Bot endpoint here.
    });

    // Function to gather data from all steps
    function gatherFormData() {
      const data = {};

      // Basic numeric fields
      data.assignedJobs    = document.getElementById("assignedJobs").value;
      data.completedJobs   = document.getElementById("completedJobs").value;
      data.rescheduledJobs = document.getElementById("rescheduledJobs").value;
      data.canceledJobs    = document.getElementById("canceledJobs").value;

      data.extraWork       = document.getElementById("extraWork").value;
      data.firstJobStart   = document.getElementById("firstJobStart").value;
      data.firstJobArrival = document.getElementById("firstJobArrival").value;
      data.lastJobStart    = document.getElementById("lastJobStart").value;
      data.lastJobArrival  = document.getElementById("lastJobArrival").value;

      // Gather dynamic comments
      data.assignedJobsComments    = gatherComments("assignedJobs", parseInt(data.assignedJobs));
      data.completedJobsComments   = gatherComments("completedJobs", parseInt(data.completedJobs));
      data.rescheduledJobsComments = gatherComments("rescheduledJobs", parseInt(data.rescheduledJobs));
      data.canceledJobsComments    = gatherComments("canceledJobs", parseInt(data.canceledJobs));

      return data;
    }

    function gatherComments(fieldPrefix, count) {
      const commentsArr = [];
      for (let i = 1; i <= count; i++) {
        const name = `${fieldPrefix}Comment${i}`;
        const textarea = document.getElementsByName(name)[0];
        commentsArr.push(textarea ? textarea.value : "");
      }
      return commentsArr;
    }
  </script>

</body>
</html>