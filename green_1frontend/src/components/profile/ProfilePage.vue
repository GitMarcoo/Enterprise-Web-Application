<template>
  <div class="mt-5 mb-5 profileBody">
    <form>
      <div class="container text-center">
        <div class="row align-items-start">
          <div class="col">
            <div class="container">
              <div class="container-md">
                <img v-if="profile.img_path" :src="profile.img_path" class="pfp" alt="...">
                <img v-else src="@/assets/photo/profielfoto-silhouet.jpg" class="pfp" alt="...">
                <UploadImageComponent class="uploadImageSpace" ref="uploadImage" :imagePath="profile.img_path"
                                      @image-previewed="handleImagePreviewed"
                                      @image-cleared="handleImageCleared"/>
<!--                <div class="mb-3" style="margin: 1rem;">-->
<!--                  <input type="file" accept="image/*" class="form-control" id="inputGroupFile02" @change="onChange">-->
<!--                </div>-->
                <div class="mb-3">
                  <label for="inputName" class="form-label">First Name: </label>
                  <input type="text" autocomplete="off" class="form-control" id="inputFirstName"
                         v-model="profile.first_name"
                         required>
                  <div v-if="isFirstNameEmpty" class="invalid-message border mt-1 error">
                    Name is required
                  </div>
                </div>
                <div class="mb-3">
                  <label for="inputName" class="form-label">Last Name: </label>
                  <input type="text" autocomplete="off" class="form-control" id="inputLastName"
                         v-model="profile.last_name"
                         required>
                  <div v-if="isLastNameEmpty" class="invalid-message border mt-1 error">
                    Name is required
                  </div>
                </div>
                <div class="mb-3">
                  <label for="inputName" class="form-label">Username: </label>
                  <input type="text" autocomplete="off" class="form-control" id="inputUserName"
                         v-model="profile.username"
                         required>
                  <div v-if="isUserNameEmpty" class="invalid-message border mt-1 error">
                    Username is required
                  </div>
                </div>
                <div class="mb-3">
                  <label for="inputBirthday" class="form-label">Birthday: </label>
                  <input type="date" min="1900-01-01" class="form-control" id="inputBirthday"
                         v-model="profile.date_of_birth"
                         required>
                  <div v-if="isBirthEmpty" class="invalid-message border mt-1 error">
                    Birthday is required
                  </div>
                </div>
                <div class="mb-3">
                  <label for="inputOccupation" class="form-label">Occupation: </label>
                  <input type="text" autocomplete="off" class="form-control" id="inputOccupation"
                         v-model="profile.occupation" aria-required="true" required placeholder="Enter your occupation...">
                </div>
                <div v-if="isOccupationEmpty" class="invalid-message border mt-1 error">
                  Occupation is required
                </div>
                <button @click="saveEdit" id="save-btn" :disabled="!isProfileChanged(this.profile, this.oldProfileData)" type="submit" class="btn btn-primary editButton">Save</button>
                <button @click="cancelEdit" id="cancel-btn" :disabled="!isProfileChanged(this.profile, this.oldProfileData)" type="button" class="btn btn-primary editButton">Cancel</button>
              </div>
            </div>
          </div>
          <div class="col">
            <div class="mb-3">
              <label for="inputBio" class="form-label">Bio: </label>
              <textarea class="form-control" id="inputBio" autocomplete="off"
                        style="height: 25rem; width: 100%; resize: none" v-model="profile.bio" required placeholder="Tell us something about you!"></textarea>
              <div v-if="isBioEmpty" class="invalid-message border mt-1 error">
                Bio is required
              </div>
            </div>
          </div>
          <div class="col">
            <button @click.prevent="showResults" type="button" class="btn btn-primary resultButton">See Quiz Results
            </button>
            <div>
              <h5>Your Action Plans</h5>
              <ProfileActionPlans />
            </div>
          </div>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import ProfileActionPlans from '@/components/profile/ProfileActionPlans.vue'

import { useToast } from 'vue-toast-notification'
import UploadImageComponent from '@/components/UploadImageComponent'
/**
 * ProfilePage component
 * This is the page is used so users view their profile information
 * and make changes to them.
 */

export default {
  name: 'ProfilePage',
  inject: ['usersServices', 'sessionService'],
  components: {
    ProfileActionPlans,
    UploadImageComponent
  },
  data () {
    return {
      user: null,
      profile: {
        first_name: '',
        last_name: '',
        occupation: '',
        username: '',
        date_of_birth: null,
        bio: '',
        img_path: null
      },
      selectedOption: '',
      sdgGoals: [
        {
          id: 1,
          sdgId: 1,
          title: 'No Poverty'
        },
        {
          id: 2,
          sdgId: 2,
          title: 'Zero Hunger'
        },
        {
          id: 3,
          sdgId: 3,
          title: 'Good Health and Well Being'
        },
        {
          id: 4,
          sdgId: 4,
          title: 'Quality Education'
        },
        {
          id: 5,
          sdgId: 5,
          title: 'Gender Equality'
        },
        {
          id: 6,
          sdgId: 6,
          title: 'Clean Water and Sanitation'
        },
        {
          id: 7,
          sdgId: 7,
          title: 'Affordable and Clean Energy'
        },
        {
          id: 8,
          sdgId: 8,
          title: 'Decent Work and Economic Growth'
        },
        {
          id: 9,
          sdgId: 9,
          title: 'Industry, Innovation, and Infrastructure'
        },
        {
          id: 10,
          sdgId: 10,
          title: 'Reduced Inequality'
        },
        {
          id: 11,
          sdgId: 11,
          title: 'Sustainable Cities and Communities'
        },
        {
          id: 12,
          sdgId: 12,
          title: 'Responsible Consumption and Production'
        },
        {
          id: 13,
          sdgId: 13,
          title: 'Climate Action'
        },
        {
          id: 14,
          sdgId: 14,
          title: 'Life Below Water'
        },
        {
          id: 15,
          sdgId: 15,
          title: 'Life on Land'
        },
        {
          id: 16,
          sdgId: 16,
          title: 'Peace, Justice, and Strong Institutions'
        },
        {
          id: 17,
          sdgId: 17,
          title: 'Partnerships for the Goals'
        }
      ],
      oldProfileData: {
        first_name: '',
        last_name: '',
        occupation: '',
        username: '',
        date_of_birth: null,
        bio: '',
        img_path: null
      },
      showGoalSDGEmpty: false,
      showGoalSDGLimit: false,
      showGoalSDGDup: false,
      showQuizResultVal: false
    }
  },
  /**
   * Function will create a copy of the profile when the page is loaded
   */
  async created () {
    try {
      // Wait for the asynchronous operation to complete
      const account = await this.sessionService.currentAccount
      this.user = await this.usersServices.asyncFindById(account.user_id)
      this.profile = { ...this.user }
      // Create a copy of the profile
      this.oldProfileData = { ...this.profile }
    } catch (error) {
      console.error('Error fetching profile data:', error)
    }
  },
  methods: {
    formatDateForBE (date) {
      const dateParts = date.split('-')
      if (dateParts.length === 3) {
        const year = parseInt(dateParts[0], 10)
        const month = parseInt(dateParts[1], 10)
        const day = parseInt(dateParts[2], 10)
        if (!isNaN(year) && !isNaN(month) && !isNaN(day)) {
          return { year, month, day }
        }
      }
      return null // Handle invalid input gracefully
    },
    handleImagePreviewed (newImagePath) {
      this.profile.img_path = newImagePath
      // Here you can also make an API call to update the user's profile photo
    },

    handleImageCleared () {
      this.profile.img_path = null
      // Handle the clearing of the photo in the profile, maybe reset to a default
    },
    update: function (attribute, event) {
      attribute = event.target.value
    },
    /**
     * Function to show the newly uploaded profile picture
     * @param e
     */
    onChange (e) {
      // Changes the image to the newly inputted image
      const file = e.target.files[0]
      this.item.profilePic = URL.createObjectURL(file)
      this.profile.photo = this.item.profilePic
    },
    /**
     * Function will show the quiz results
     */
    showResults () {
      this.$router.push({ path: '/results' })
    },
    /**
     * Function will undo any changes that has been made
     */
    cancelEdit () {
      // Check if the user wants to undo the changes
      if (confirm('Are you sure you want to undo your changes?') === true) {
        this.profile = { ...this.oldProfileData }
        useToast().success('Changes have been undone')
      }
    },
    /**
     * Function to save changes
     */
    async saveEdit () {
      try {
        // Check if firstname, lastname, birth, occupation, bio and goals are not empty
        if (this.profile.first_name === '' ||
          this.profile.last_name === '' ||
          this.profile.date_of_birth === '' ||
          this.profile.occupation === '' ||
          this.profile.bio === '') {
          return alert('Some of the fields are empty')
        } else {
          // Check if the user wants to save the changes
          if (confirm('Are you sure you want to save changes?') === true) {
            // Overwrite old data with newely input data
            try {
              const user = {
                first_name: this.profile.first_name,
                last_name: this.profile.last_name,
                occupation: this.profile.occupation,
                username: this.profile.username,
                date_of_birth: this.profile.date_of_birth,
                bio: this.profile.bio
              }
              if (this.profile.img_path !== this.oldProfileData.img_path) {
                user.img_path = await this.$refs.uploadImage.uploadImage()
              }
              // Wait for the asynchronous operation to complete
              const savedProfile = await this.usersServices.asyncUpdateProfile(JSON.stringify(user), this.user.user_id)
              // Check if the profile has been saved
              if (savedProfile) {
                if (this.profile.username !== this.oldProfileData.username) {
                  this.profile = { ...savedProfile }
                  this.oldProfileData = { ...this.profile }
                  useToast().success('Changes have been saved, please login again!')
                  await new Promise(resolve => setTimeout(resolve, 5000))
                  this.$router.push({ path: '/sign-out' })
                } else {
                  this.profile = { ...savedProfile }
                  this.oldProfileData = { ...this.profile }
                  useToast().success('Changes have been saved!')
                }
              } else {
                // If the profile has not been saved, show an error
                useToast().error('Failed to save changes. Please check the console for errors.')
              }
            } catch (error) {
              console.error('Error saving profile data:', error)
              useToast().error('Failed to save changes. Please check the console for errors.')
            }
            // Creates a new copy of the profile
            this.oldProfileData = { ...this.profile }
          }
        }
      } catch (error) {
        console.error('Error saving profile data:', error)
      }
    },
    isProfileChanged (profileA, profileB) {
      return !this.deepEqual(profileA, profileB)
    },
    deepEqual (obj1, obj2) {
      if (obj1 === obj2) return true

      if (typeof obj1 !== 'object' || obj1 === null || typeof obj2 !== 'object' || obj2 === null) {
        return false
      }

      const keys1 = Object.keys(obj1)
      const keys2 = Object.keys(obj2)

      if (keys1.length !== keys2.length) {
        return false
      }

      for (const key of keys1) {
        if (!keys2.includes(key) || !this.deepEqual(obj1[key], obj2[key])) {
          return false
        }
      }

      return true
    }
  },
  computed: {
    /**
     * Validation checks
     * @return {boolean}
     */
    isFirstNameEmpty () {
      // Check if the name is an empty string
      return this.profile.first_name === ''
    },
    isLastNameEmpty () {
      // Check if the name is an empty string
      return this.profile.last_name === ''
    },
    isUserNameEmpty () {
      // Check if the name is an empty string
      return this.profile.last_name === ''
    },
    // Check if the birth is an empty string
    isBirthEmpty () {
      return this.profile.date_of_birth === ''
    },
    // Check if the occupation is an empty string
    isOccupationEmpty () {
      return this.profile.occupation === ''
    },
    // Check if the bio is an empty string
    isBioEmpty () {
      return this.profile.bio === ''
    }
  }
}
</script>

<style scoped>
.pfp {
  width: 7.5rem;
  height: 7.5rem;
  border-radius: 60px;
}

.resultButton {
  margin: 0.5rem;
  width: 10rem;
  border-radius: 20px;
  align-self: center;
}

.addButton {
  margin: 0.5rem;
  width: 10rem;
  border-radius: 20px;
  align-self: center;
}

.editButton {
  margin: 0.5rem;
  width: 7.5rem;
  border-radius: 20px;
  align-self: center;
}

.goalContainer {
  max-height: 25rem;
  overflow: auto
}

.deleteGoalButton {
  margin-left: auto;
  width: auto;
  height: 50%;
  align-self: center;
}

.deleteGoalButton:hover {
  cursor: pointer;
}

.goalImage {
  width: auto;
  height: 80%;
}

.goals {
  display: flex;
  flex-direction: row;
  width: 100%;
  height: 5rem;
}

.goalTitle {
  margin-top: 1rem;
  margin-left: auto;
  text-align: center;
  align-self: center;
}

.selectGoal:hover {
  cursor: pointer;
}

.error {
  color: red;
}
</style>
