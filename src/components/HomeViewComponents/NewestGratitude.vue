<template>
  <ul>
    <li v-for="gratitude in gratitudesToday.slice(-1)" :key="gratitude.id">
      <form
        :id="'show-one-of-todays-gratitudes-with-ID: ' + gratitude.id"
        :name="'show-one-of-todays-gratitudes-with-ID :' + gratitude.id"
      >
        <label :for="'ID_' + gratitude.id">
          <textarea
            :value="gratitude.text"
            placeholder="Heute bin ich dankbar für..."
            :id="'ID_' + gratitude.id"
            ref="textarea"
            @click="$router.push('/dankbarkeiten')"
          ></textarea>
        </label>
      </form>
    </li>
    <!-- Empty textarea for new gratitude: only visible if no gratitudes have been written yet on any given day -->
    <li v-if="noGratitudesYet">
      <form id="add-new-gratitude-for-today" name="add-new-gratitude-for-today">
        <label>
          <textarea
            placeholder="Heute bin ich dankbar für..."
            @click="$router.push('/dankbarkeiten')"
          ></textarea>
        </label>
      </form>
    </li>
  </ul>
</template>

<script>
import { mapStores } from 'pinia'
import { useGratitudesStore } from '../../stores/gratitudes'

export default {
  data() {
    return {}
  },
  computed: {
    ...mapStores(useGratitudesStore),
    gratitudesToday: function () {
      return this.gratitudesStore.gratitudes.filter((gratitude) => {
        const createdAt = new Date(gratitude.createdAt)
        const rightNow = new Date()
        return (
          createdAt.getDate() === rightNow.getDate() &&
          createdAt.getMonth() === rightNow.getMonth() &&
          createdAt.getFullYear() === rightNow.getFullYear()
        )
      })
    },
    noGratitudesYet() {
      return this.gratitudesToday.length === 0
    }
  },
  methods: {
    resize(event) {
      const element = event.target
      element.style.height = 'auto'
      element.style.height = element.scrollHeight + 'px'
    }
  },
  mounted() {
    // Adjusts textarea-sizing when the app is mounted
    this.$nextTick(() => {
      const textarea = this.$refs.textarea
      if (textarea) {
        if (Array.isArray(textarea)) {
          textarea.forEach((textarea) => {
            this.resize({ target: textarea })
          })
        } else {
          this.resize({ target: textarea })
        }
      }
    })
  }
}
</script>

<style scoped>
.timeline-view-links {
  display: flex;
}

ul {
  color: #4f65df;
  list-style-type: none;
  padding-left: 0;
}

li {
  position: relative;
  margin-bottom: 1em;
}

form::before {
  content: url(../../public/header-star-green.png);
  position: absolute;
  left: -10rem;
  top: -8.4rem;
  transform: scale(0.13);
}

textarea {
  color: #5ce1e6;
  background-color: rgba(92, 225, 230, 0.1);
  min-height: 10px;
  width: 100%;
  padding: 1rem;
  resize: none;
  overflow: hidden;
  border: none;
  border-radius: 4px;
}

textarea:hover {
  background-color: rgba(92, 225, 230, 0.4);
}

::placeholder {
  color: rgba(92, 225, 230, 0.4);
}
</style>
