<template>
  <div class="container">
    <div class="left-part">
      <div class="profile">
        <img class="arrow" src="./../src/assets/img/arrow.png" alt="" />
        <h1>TROOD.</h1>
        <h5>Profile</h5>
      </div>

      <div class="projects">
        <h3>Projects:</h3>
        <div class="square">
          <h5>Create project</h5>
        </div>
      </div>

      <div class="tasks">
        <h3>Tasks:</h3>
        <div class="square">
          <h5>Create task</h5>
        </div>
      </div>
    </div>

    <div class="right-part">
      <div>
        <!-- Avatar container -->
        <div class="avatar-container" @click="triggerFileInput">
          <!-- Background picture (round background) -->
          <img
            v-if="!userAvatarPreview"
            src="./../src/assets/img/circle.png"
            alt="Circle"
            class="avatar-background"
          />

          <!-- Avatar picture -->
          <img
            v-if="userAvatarPreview"
            :src="userAvatarPreview"
            alt="Avatar"
            class="avatar-overlay"
          />

          <!-- Standard avatar (photo.png), which is always on top of the background -->
          <img
            v-if="!userAvatarPreview"
            src="./../src/assets/img/photo.png"
            alt="Default Avatar"
            class="avatar-img"
          />

          <!-- Hidden input for file upload -->
          <input
            type="file"
            ref="fileInput"
            @change="onFileChange"
            accept="image/jpeg, image/png, image/jpg"
            style="display: none"
          />
        </div>

        <!-- Form fields -->
        <input
          type="text"
          v-model="userName"
          placeholder="Name*"
          @blur="validateUserName"
        />
        <p class="error" v-if="errors.userName">{{ errors.userName }}</p>

        <input
          type="text"
          v-model="userLastName"
          placeholder="Lastname*"
          @blur="validateUserLastName"
        />
        <p class="error" v-if="errors.userLastName">
          {{ errors.userLastName }}
        </p>

        <input
          type="text"
          v-model="userPosition"
          placeholder="Job Title"
          @blur="validateUserPosition"
        />
        <p class="error" v-if="errors.userPosition">
          {{ errors.userPosition }}
        </p>

        <input
          type="text"
          v-model="userPhone"
          placeholder="Phone*"
          @blur="validateUserPhone"
        />
        <p class="error" v-if="errors.userPhone">{{ errors.userPhone }}</p>

        <!-- Email fields-->
        <input
          type="email"
          v-model="userEmail"
          placeholder="Email"
          @blur="validateUserEmail"
        />
        <p class="error" v-if="errors.userEmail">{{ errors.userEmail }}</p>

        <input type="text" v-model="userAddress" placeholder="Address" />
        <p class="error" v-if="errors.userAddress">{{ errors.userAddress }}</p>

        <!-- Pitch fields -->
        <input
          v-model="userPitch"
          placeholder="Pitch"
          @blur="validateUserPitch"
        />
        <p class="error" v-if="errors.userPitch">{{ errors.userPitch }}</p>
        <div>
          <!-- Error fot interests -->
          <p class="error" v-if="errors.userInterests">
            {{ errors.userInterests }}
          </p>
          <p class="mandatory-note">* - required field</p>
          <!-- Field for entering text of interest -->
          <input
            type="text"
            v-model="newInterest"
            placeholder="Enter a new interest"
          />
          <p class="error" v-if="errors.newInterest">
            {{ errors.newInterest }}
          </p>

          <div>
            <label>Interests:</label>
            <button @click="toggleTagList" class="add-tag-btn">+</button>

            <!-- List of tags -->
            <div v-if="showTagList" class="tag-list">
              <div
                v-for="(tag, index) in availableTags"
                :key="index"
                class="tag-item"
                @click="addInterest(tag)"
              >
                {{ tag }}
              </div>
            </div>
            <!--
            <p class="error" v-if="errors.userInterests">
              {{ errors.userInterests }} -->
            <!-- </p> -->
          </div>
        </div>

        <!-- Show selected interests -->
        <div class="selected-tags">
          <span
            v-for="(interest, index) in userInterests"
            :key="index"
            class="selected-tag"
          >
            {{ interest }}
            <button @click="removeInterest(interest)" class="remove-btn">
              x
            </button>
          </span>
        </div>

        <!-- Error by interests -->
        <p class="error" v-if="errors.userInterests">
          {{ errors.userInterests }}
        </p>

        <!-- Profile links -->
        <div class="profile-link-section">
          <div class="links-btn">
            <label>Links:</label>
            <button class="add-profile-link-btn" @click="addProfileLink">
              +
            </button>
          </div>

          <div
            v-for="(link, index) in profileLinks"
            :key="index"
            class="profile-link"
          >
            <div class="input-group">
              <input
                type="text"
                v-model="link.siteName"
                placeholder="Site name"
              />
              <input
                type="text"
                v-model="link.link"
                placeholder="Link"
                @blur="validateLink(link)"
              />
              <img
                src="./../src/assets/img/delete.png"
                alt="Delete"
                @click="removeProfileLink(index)"
                class="delete-icon"
              />
            </div>
            <p class="error" v-if="link.errors">{{ link.errors }}</p>
          </div>
        </div>

        <!-- Profile Visibility -->
        <div class="profile-visibility-container">
          <label>Show your profile in Launchpad?</label>
          <div class="radio-group">
            <div>

            </div>
            <label class="radio-label">
              <input
                type="radio"
                v-model="profileVisibility"
                value="Private"
                class="radio-button"
              />
              <span>Private</span>
            </label>
            <label class="radio-label">
              <input
                type="radio"
                v-model="profileVisibility"
                value="Public"
                class="radio-button"
              />
              <span>Public</span>
            </label>
          </div>
        </div>

        <!-- Buttons -->
        <button @click="saveData()">Save</button>
        <button @click="cancelChanges()">Cancel</button>

        <!-- Successful save message -->
        <div v-if="successMessage" class="success-message">
          <p>{{ successMessage }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: [],
      successMessage: "",
      userName: "",
      userLastName: "",
      userPosition: "",
      userPhone: "",
      userAddress: "",
      userInterests: [],
      newInterest: "",
      userProfileLink: "",
      profileLinks: [],
      userAvatarPreview: "",
      userEmail: "",
      userPitch: "",
      profileVisibility: "Private",
      errors: {
        userName: "",
        userLastName: "",
        userPosition: "",
        userPhone: "",
        userAddress: "",
        userInterests: "",
        userProfileLink: "",
        userAvatar: "",
        newInterest: "",
      },
      availableTags: [
        "JavaScript",
        "vue.js",
        "react.js",
        "node.js",
        "html",
        "css",
        "python",
        "c++",
        "java",
        "mongo DB",
        "MySQL",
      ],
      showTagList: false,
    };
  },

  mounted() {
    this.loadDataFromLocalStorage();
  },

  methods: {
    // Activate the hidden input field to select a file
    triggerFileInput() {
      this.$refs.fileInput.click();
    },

    // File change handler
    onFileChange(event) {
      const file = event.target.files[0];
      if (file && file.type.startsWith("image/")) {
        const reader = new FileReader();
        reader.onload = () => {
          this.userAvatarPreview = reader.result;
          this.saveData();
        };
        reader.readAsDataURL(file);
      } else {
        this.errors.userAvatar = "Select an image";
      }
    },
    // Loading data from localStorage
    loadDataFromLocalStorage() {
      try {
        const savedData = localStorage.getItem("userData");
        if (savedData) {
          const parsedData = JSON.parse(savedData);
          this.userName = parsedData.userName || "";
          this.userLastName = parsedData.userLastName || "";
          this.userPosition = parsedData.userPosition || "";
          this.userPhone = parsedData.userPhone || "";
          this.userAddress = parsedData.userAddress || "";
          this.userInterests = parsedData.userInterests || [];
          this.profileLinks = parsedData.profileLinks || [];
          this.userAvatarPreview = parsedData.userAvatarPreview || "";
          this.userEmail = parsedData.userEmail || "";
          this.userPitch = parsedData.userPitch || "";
          this.profileVisibility = parsedData.profileVisibility || "Private";
        }
      } catch (e) {
        console.error("Error loading data from localStorage:", e);
      }
    },

    // Saving data to localStorage
    saveData() {
      this.validateUserName();
      this.validateUserLastName();
      this.validateUserPosition();
      this.validateUserPhone();
      this.validateUserEmail();
      this.validateUserPitch();
      this.validateInterests();

      if (Object.values(this.errors).some((error) => error !== "")) {
        return;
      }

      // Create a user data object
      const userData = {
        userName: this.userName,
        userLastName: this.userLastName,
        userPosition: this.userPosition,
        userPhone: this.userPhone,
        userAddress: this.userAddress,
        userInterests: this.userInterests,
        profileLinks: this.profileLinks,
        userAvatarPreview: this.userAvatarPreview,
        userEmail: this.userEmail,
        userPitch: this.userPitch,
        profileVisibility: this.profileVisibility,
      };

      // Saving data in localStorage
      localStorage.setItem("userData", JSON.stringify(userData));

      this.successMessage = "Data saved successfully!";
      setTimeout(() => (this.successMessage = ""), 5000);

      // this.clearFields();
    },

    // Cancel changes
    cancelChanges() {
      this.loadDataFromLocalStorage();
    },

    // Toggle Tag List Visibility
    toggleTagList() {
      this.showTagList = !this.showTagList;
    },

    // Adding Interest
    addInterest(tag) {
      if (!this.userInterests.includes(tag)) {
        this.userInterests.push(tag);
      }
    },

    // Removing a Lead
    removeInterest(interest) {
      const index = this.userInterests.indexOf(interest);
      if (index > -1) {
        this.userInterests.splice(index, 1);
      }
    },

    // Adding a new profile link
    addProfileLink() {
      this.profileLinks.push({ siteName: "", link: "", errors: "" });
    },

    validateLink(link) {
      const urlPattern =
        /^(https?:\/\/)?([\da-z.-]+)\.([a-z.]{2,6})([/\w .-]*)*\/?$/;
      if (!link.link) {
        link.errors = "Link is required";
      } else if (!urlPattern.test(link.link)) {
        link.errors = "Invalid URL format";
      } else {
        link.errors = ""; // We remove the error if the validation was successful
      }
    },

    // Removing a profile link
    removeProfileLink(index) {
      this.profileLinks.splice(index, 1);
    },

    // Add a new interest manually
    addInterestManual() {
      this.validateNewInterest(); // Checking for errors
      if (this.errors.newInterest) return; // If there is an error, do not add it

      this.userInterests.push(this.newInterest); // Adding interest
      this.newInterest = ""; // Clearing the input field
    },

    // Validation of other fields (first name, last name, phone, etc.)
    validateUserName() {
      const regex = /^[a-zA-Zа-яА-Я\s]+$/;
      if (!this.userName) {
        this.errors.userName = "*No name entered";
      } else if (this.userName.length < 2 || this.userName.length > 50) {
        this.errors.userName = "Length: from 2 to 50 characters";
      } else if (!regex.test(this.userName)) {
        this.errors.userName = "Only letters and spaces are allowed";
      } else {
        this.errors.userName = "";
      }
    },

    validateUserLastName() {
      const regex = /^[a-zA-Zа-яА-Я\s]+$/;
      if (!this.userLastName) {
        this.errors.userLastName = "*Last name not entered";
      } else if (
        this.userLastName.length < 2 ||
        this.userLastName.length > 50
      ) {
        this.errors.userLastName = "om 2 to 50 characters";
      } else if (!regex.test(this.userLastName)) {
        this.errors.userLastName = "Only letters and spaces are allowed";
      } else {
        this.errors.userLastName = "";
      }
    },

    validateUserPosition() {
      if (this.userPosition.length > 100) {
        this.errors.userPosition =
          "The position cannot be more than 100 characters long";
      } else {
        this.errors.userPosition = "";
      }
    },

    validateUserEmail() {
      const regex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
      if (!this.userEmail) {
        this.errors.userEmail = "*Email not entered";
      } else if (!regex.test(this.userEmail)) {
        this.errors.userEmail = "Invalid email format";
      } else {
        this.errors.userEmail = "";
      }
    },

    validateUserPitch() {
      if (this.userPitch.length > 300) {
        this.errors.userPitch = "Pitch cannot be more than 300 characters long";
      } else {
        this.errors.userPitch = "";
      }
    },

    validateUserPhone() {
      const regex = /^\+(\d{1,3})\d{10,14}$/;
      if (!this.userPhone) {
        this.errors.userPhone = "*Phone number not entered";
      } else if (!regex.test(this.userPhone)) {
        this.errors.userPhone = "Only numbers and the "+" symbol at the beginning. Length: 10 to 15 characters";
      } else {
        this.errors.userPhone = "";
      }
    },

    validateInterests() {
      if (this.userInterests.length > 10) {
        this.errors.userInterests = "Maximum 10 interests";
      } else {
        this.errors.userInterests = "";
      }
    },

    validateNewInterest() {
      if (!this.newInterest) {
        this.errors.newInterest = "Interest cannot be empty";
      } else if (this.userInterests.includes(this.newInterest)) {
        this.errors.newInterest = "This interest has already been added";
      } else {
        this.errors.newInterest =
          "Only numbers and the " +
          " symbol at the beginning. Length: 10 to 15 characters";
      }
    },
  },
};
</script>

<style scoped>
.container {
  display: flex;
  justify-content: space-between;
  height: 100vh;
}

.profile {
  display: flex;
  /* gap: 10px; */
  align-items: center;
}

.arrow {
  width: 39px;
  height: 39px;
}

/* Left Side Styles */
.left-part {
  align-items: flex-start;
  width: 60%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: left;
  padding: 20px;
}
.square {
  width: 240px;
  height: 180px;
  background-color: #ddd;
  margin: 20px 0;
  display: flex;
  justify-content: left;
  align-items: center;
  border-radius: 5px;
  padding-left: 10px;
}

/* Right Side Styles */
.right-part {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  padding: 20px;
  background: linear-gradient(to bottom, #e9f2f3, #a6c5e3);
  overflow-y: auto;
  border-radius: 15px;
  width: 400px;
  height: 100%;
  margin-right: 20px;
}

.avatar-container {
  position: relative;
  display: inline-block;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background-color: #f0f0f0;
  overflow: hidden;
  margin-left: 4vw;
}

.avatar-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 50%;
}

.avatar-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 50%;
}

input[type="file"] {
  display: none;
}

/*Form styles */
input {
  margin-right: 100px;
  display: block;
  width: 100%;
  margin-bottom: 10px;
  border-radius: 3px;
  border: 1px solid silver;
  outline: none;
  padding: 10px 15px;
  background: #fafafa;
  color: #333;
}

button {
  border: 0;
  border-radius: 5px;
  outline: none;
  padding: 10px 15px;
  background: #1235aa;
  color: #c9c3dd;
  font-weight: bold;
  cursor: pointer;
  transition: transform 500ms ease;
  margin-right: 10px;
}

button:hover {
  transform: translateY(-5px);
}

.success-message {
  margin-top: 20px;
  background-color: #d4edda;
  color: #155724;
  border: 1px solid #c3e6cb;
  padding: 10px;
  border-radius: 5px;
  font-size: 16px;
}

.success-message p {
  margin: 0;
}

.error {
  color: red;
  font-size: 14px;
  margin-top: 5px;
  display: block;
}

.mandatory-note {
  font-size: 14px;
  color: #333;
  margin-top: 5px;
  font-style: italic;
}

.profile-visibility-container {
  margin-top: 15px;
}

.radio-group {
  display: flex;
  /* gap: 10px; */
}

.radio-label {
  display: flex;
  align-items: center;
  /* gap: 8px; */
}

.radio-button {
  color: #007bff;
  width: 15px;
  height: 15px;
  border-radius: 50%;
  cursor: pointer;
}

input[type="radio"]:checked {
  background-color: #007bff;
  border-color: #007bff;
}

.radio-label span {
  font-size: 14px;
}

.add-tag-btn,
.add-profile-link-btn {
  background: none;
  border: none;
  font-size: 20px;
  color: #007bff;
  cursor: pointer;
  padding: 0;
  margin-left: 10px;
}

.tag-list {
  display: flex;
  flex-wrap: wrap;
  /* gap: 10px; */
  margin-top: 10px;
}

.tag-item {
  padding: 8px 16px;
  border: 2px solid #007bff;
  border-radius: 20px;
  background-color: #f0f8ff;
  cursor: pointer;
}

.tag-item:hover {
  background-color: #007bff;
  color: white;
}

.selected-tags {
  margin-top: 10px;
}

.selected-tag {
  display: inline-block;
  padding: 5px 10px;
  background-color: #e0f7fa;
  border: 1px solid #007bff;
  border-radius: 20px;
  margin-right: 10px;
}

.remove-btn {
  background: none;
  border: none;
  color: red;
  cursor: pointer;
  font-size: 12px;
  margin-left: 5px;
}

.profile-link-section {
  margin-top: 20px;
}

.links-btn {
  display: flex;
}

.profile-link {
  align-items: center;
  margin-bottom: 10px;
}

.input-group {
  display: flex;
  /* gap: 10px; */
  margin-bottom: 5px;
}

.profile-link input {
  margin-right: 10px;
}

.delete-icon {
  cursor: pointer;
  width: 20px;
  height: 20px;
}
</style>
