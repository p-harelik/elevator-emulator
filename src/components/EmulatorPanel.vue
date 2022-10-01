<template>
  <div class="container">
    <div class="wrap">
      <div class="container__underlay">
        <floor-item v-for="n in floors.length" :key="n" />
      </div>
      <button-group :floors="floors" @addCall="handlerCallOfElevator" />
      <elevator-shaft
        v-for="elevator in elevators"
        :key="elevator.id"
        :floorHeight="floorHeight"
        :elevator="elevator"
        class="container__lift-shaft"
      />
    </div>
  </div>
</template>

<script>
import FloorItem from "@/components/FloorItem";
import ButtonGroup from "@/components/ButtonsGroup";
import ElevatorShaft from "@/components/ElevatorShaft";
import DefaultValues from "@/emulator.config";
export default {
  name: "EmulatorPanel",
  components: { ElevatorShaft, ButtonGroup, FloorItem },
  data() {
    return {
      floors: [],
      elevators: [],
      callQueue: [],
    };
  },
  computed: {
    floorsCount() {
      return this.floors.length;
    },
    floorHeight() {
      return 100 / this.floorsCount;
    },
    freeElevators() {
      return this.elevators.filter((elevator) => elevator.status === "free");
    },
    waitElevators() {
      return this.elevators.filter((elevator) => elevator.status === "wait");
    },
    moveElevators() {
      return this.elevators.filter((elevator) => elevator.status === "move");
    },
    activeFloors() {
      return this.floors.filter((floor) => floor.isActiveButton);
    },
  },
  watch: {
    callQueue() {
      this.saveActiveFloors();
      if (this.freeElevators.length) {
        const targetFloor = this.callQueue.shift();
        const targetElevator = this.getTargetElevator(
          this.freeElevators,
          targetFloor
        );
        this.moveElevator(targetElevator, targetFloor);
      }
    },
  },
  methods: {
    handlerCallOfElevator(floorNumber) {
      setTimeout(() => {
        if (!this.isElevatorOnTargetFloor(floorNumber)) {
          this.floors[floorNumber - 1].isActiveButton = true;
          this.callQueue = [...this.callQueue, floorNumber];
        }
      }, 0);
    },
    isElevatorOnTargetFloor(floorNumber) {
      return this.elevators.find(
        (elevator) =>
          elevator.startFloorNumber === floorNumber &&
          elevator.status !== "move"
      );
    },
    getTargetElevator(elevators, floor) {
      const arr = [...elevators].sort(
        (el1, el2) =>
          Math.abs(el1.startFloorNumber - floor) -
          Math.abs(el2.startFloorNumber - floor)
      );
      return arr[0];
    },

    moveElevator(elevator, floor) {
      elevator.endFloorNumber = floor;
      elevator.status = "move";
      const moveTime =
        elevator.speed * Math.abs(elevator.startFloorNumber - floor);

      setTimeout(() => {
        elevator.status = "wait";
        this.floors[floor - 1].isActiveButton = false;
        elevator.startFloorNumber = floor;

        setTimeout(() => {
          elevator.status = "free";
          this.saveActiveFloors();
          this.saveElevators();

          if (this.callQueue.length) {
            this.moveElevator(elevator, this.callQueue.shift());
          }
        }, elevator.waitTime);
      }, moveTime);
    },
    generateFloors(floorsCount) {
      const result = [];

      for (let i = 1; i <= floorsCount; i += 1) {
        result.push({
          number: i,
          isActiveButton: false,
        });
      }
      return result;
    },
    generateElevators(elevatorsCount) {
      const result = [];
      for (let i = 1; i <= elevatorsCount; i += 1) {
        const elevator = {
          id: i,
          startFloorNumber: 1,
          endFloorNumber: 1,
          speed: 1000,
          waitTime: 3000,
          status: "free",
        };
        result.push(elevator);
      }

      return result;
    },
    saveActiveFloors() {
      sessionStorage.setItem("activeFloors", JSON.stringify(this.activeFloors));
    },
    saveElevators() {
      console.log(this.elevators);
      sessionStorage.setItem("elevators", JSON.stringify(this.elevators));
    },
  },
  mounted() {
    if (sessionStorage.getItem("elevators")) {
      const elevators = JSON.parse(sessionStorage.getItem("elevators"));
      this.elevators = elevators;
    }
    if (sessionStorage.getItem("activeFloors")) {
      const activeFloors = JSON.parse(
        sessionStorage.getItem("activeFloors")
      ).map((floor) => floor.number);
      activeFloors.forEach(this.handlerCallOfElevator);
    }
  },
  created() {
    this.floors = this.generateFloors(DefaultValues.floorsCount);
    this.elevators = this.generateElevators(DefaultValues.elevatorsCount);
  },
};
</script>

<style scoped>
.container {
  display: flex;
  min-height: 94vh;
  width: 100%;
  margin: 3vh 0;
  overflow-x: auto;
  padding-bottom: 20px;
}

.wrap {
  display: flex;
  position: relative;
}

.container__underlay {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: -1;
  width: 100%;
  display: flex;
  flex-direction: column;
}
.container__lift-shaft {
  margin: 0 10px;
}
</style>
