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
      processingQueue: [],
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
    activeFloors() {
      return this.floors.filter((floor) => floor.isActiveButton);
    },
  },
  watch: {
    processingQueue(newValue) {
      this.saveActiveFloors();
      if (this.freeElevators.length) {
        console.log(newValue);
        const targetFloor = this.processingQueue.shift();
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
        if (
          !this.isElevatorOnTargetFloor(floorNumber) &&
          !this.isFloorProcessed(floorNumber)
        ) {
          this.floors[floorNumber - 1].isActiveButton = true;
          this.processingQueue = [...this.processingQueue, floorNumber];
          this.callQueue.push(floorNumber);
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
    isFloorProcessed(floorNumber) {
      const floor = this.floors[floorNumber - 1];
      return floor.isSelectElevator;
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
        elevator.startFloorNumber = floor;
        this.saveElevators();
        this.floors[floor - 1].isActiveButton = false;
        setTimeout(() => {
          elevator.status = "free";
          this.callQueue.shift();
          this.saveElevators();
          this.saveActiveFloors();

          if (this.processingQueue.length) {
            this.moveElevator(elevator, this.processingQueue.shift());
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
          isSelectElevator: false,
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
      if (this.elevators.length === 1) {
        sessionStorage.setItem("activeFloors", JSON.stringify(this.callQueue));
      }
    },
    saveElevators() {
      if (this.elevators.length === 1) {
        sessionStorage.setItem("elevators", JSON.stringify(this.elevators));
      }
    },
  },
  mounted() {
    if (sessionStorage.getItem("elevators")) {
      this.elevators = JSON.parse(sessionStorage.getItem("elevators"));
      const waitElevators = this.elevators.filter((elevator) => {
        elevator.status = "free";
      });
      waitElevators.forEach((elevator) => {
        elevator.status = "free";
      });
    }

    if (sessionStorage.getItem("activeFloors")) {
      const activeFloors = JSON.parse(sessionStorage.getItem("activeFloors"));
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
