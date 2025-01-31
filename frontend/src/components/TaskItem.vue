<template>
  <div>
    <div class="parent">
      <div
        class="card"
        :style="cardTransformStyle"
        @mousemove="handleMouseMove"
        @mouseleave="resetCard"
      >
        <label class="custom-checkbox" :style="checkboxStyle">
          <input
            type="checkbox"
            :checked="task.completed"
            @change="updateStatus"
          />
          <span class="checkmark"></span>
        </label>

        <div class="content-box">
          <h1
            class="card-title"
            :style="{
              textDecoration: task.completed ? 'line-through' : 'none',
            }"
          >
            {{ task.name }}
          </h1>
        </div>

        <div class="date-box">
          <span class="month">{{ formattedDate.month }}</span>
          <span class="date">{{ formattedDate.day }}</span>
        </div>

        <span class="delete" @click="deleteTask">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="32"
            height="32"
            fill="#ebebeb"
            viewBox="0 0 256 256"
          >
            <path
              d="M216,48H176V40a24,24,0,0,0-24-24H104A24,24,0,0,0,80,40v8H40a8,8,0,0,0,0,16h8V208a16,16,0,0,0,16,16H192a16,16,0,0,0,16-16V64h8a8,8,0,0,0,0-16ZM96,40a8,8,0,0,1,8-8h48a8,8,0,0,1,8,8v8H96Zm96,168H64V64H192ZM112,104v64a8,8,0,0,1-16,0V104a8,8,0,0,1,16,0Zm48,0v64a8,8,0,0,1-16,0V104a8,8,0,0,1,16,0Z"
            ></path>
          </svg>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    task: Object,
    index: Number,
  },
  data() {
    return {
      hue: 0,
      rotateX: 0,
      rotateY: 0,
      bgPosX: 50,
      bgPosY: 50,
    };
  },
  created() {
    this.hue = this.generateRandomHue();
  },
  methods: {
    updateStatus() {
      this.$emit("update", this.task.id);
    },
    deleteTask() {
      this.$emit("delete", this.task.id);
    },
    generateRandomHue() {
      return Math.floor(Math.random() * 360);
    },
    handleMouseMove(event) {
      const card = event.currentTarget;
      const rect = card.getBoundingClientRect();
      const centerX = rect.left + rect.width / 2;
      const centerY = rect.top + rect.height / 2;
      const mouseX = event.clientX - centerX;
      const mouseY = event.clientY - centerY;

      const rotateAmount = 30;
      const bgShiftAmount = 30;

      this.rotateX = (-mouseY / rect.height) * rotateAmount;
      this.rotateY = (mouseX / rect.width) * rotateAmount;

      this.bgPosX = 50 + (mouseX / rect.width) * bgShiftAmount;
      this.bgPosY = 50 + (mouseY / rect.height) * bgShiftAmount;
    },
    resetCard() {
      this.rotateX = 0;
      this.rotateY = 0;
      this.bgPosX = 50;
      this.bgPosY = 50;
    },
  },
  computed: {
    cardTransformStyle() {
      return {
        filter: `hue-rotate(${this.hue}deg)`,
        transform: `rotateX(${this.rotateX}deg) rotateY(${this.rotateY}deg)`,
        backgroundPosition: `${this.bgPosX}% ${this.bgPosY}%`,
        transition:
          "transform 0.1s ease-out, background-position 0.2s ease-out",
      };
    },
    checkboxStyle() {
      return {
        filter: `hue-rotate(${this.hue}deg)`,
      };
    },
    formattedDate() {
      const date = new Date(this.task.date);
      const adjustedDate = new Date(
        date.toLocaleString("en-US", { timeZone: "UTC" })
      );

      return {
        month: adjustedDate.toLocaleString("en-US", { month: "short" }),
        day: adjustedDate.getDate(),
      };
    },
  },
};
</script>

<style scoped>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

.parent {
  height: 350px;
  width: 300px;
  padding: 20px;
  perspective: 1000px;
  position: relative;
}

.card {
  padding-top: 100px;
  transform-style: preserve-3d;
  background-image: url("https://images.pexels.com/photos/339119/pexels-photo-339119.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1");
  background-position: 50% 50%;
  background-size: cover;
  width: 100%;
  height: 100%;
  transition: all 0.1s ease-out;
  border-radius: 10px;
  position: relative;
}

.content-box {
  height: 100%;
  width: 100%;
  background-color: rgba(0, 0, 0, 0.811);
  border-radius: 0 0 10px 10px;
  box-shadow: rgba(0, 115, 255, 0.925) 0px 20px 50px -25px;
  transition: all 0.5s ease-in-out;
  padding: 50px 25px 25px 25px;
  transform-style: preserve-3d;
  display: flex;
  justify-content: center;
  align-items: center;
}

.card-title {
  display: flex;
  justify-content: flex-start;
  color: white;
  font-size: 25px;
  font-weight: 900;
  transition: all 0.5s ease-in-out;
  transform: translate3d(0px, 0px, 20px);
  padding: 1%;
  max-height: 150px;
  overflow-y: auto;
  &::-webkit-scrollbar {
    display: none;
  }
}

.delete {
  position: absolute;
  bottom: 0;
  right: 5px;
  cursor: pointer;
  transition: transform 0.3s ease-in-out;
}

.delete:hover {
  cursor: pointer;
  transform: translate3d(0px, 0px, 50px);
}

.date-box {
  position: absolute;
  top: 75px;
  left: 25px;
  height: 60px;
  width: 60px;
  background-color: rgb(0, 0, 0);
  border-radius: 10px;
  padding: 10px;
  box-shadow: rgb(0, 102, 255) 0px 20px 60px 0px, rgb(0, 200, 255);
  transform: translate3d(0px, 0px, 50px);
}

.date-box span {
  display: block;
  text-align: center;
}

.date-box .month {
  color: white;
  font-size: 11px;
  font-weight: 700;
}

.date-box .date {
  font-size: 20px;
  font-weight: 900;
  color: rgb(0, 136, 255);
}

.custom-checkbox {
  position: absolute;
  top: 10px;
  right: 10px;
  width: 24px;
  height: 24px;
  cursor: pointer;
}

.custom-checkbox input {
  opacity: 0;
  position: absolute;
  width: 0;
  height: 0;
}

.checkmark {
  width: 24px;
  height: 24px;
  display: inline-block;
  border-radius: 4px;
  border: 2px solid #ebebeb;
  transition: all 0.3s ease-in-out;
  position: relative;
}

.custom-checkbox input:checked + .checkmark {
  background-color: #ebebeb;
  border-color: #ebebeb;
  filter: inherit;
}

.custom-checkbox input:checked + .checkmark::after {
  content: "✔";
  font-size: 16px;
  color: black;
  font-weight: bold;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
