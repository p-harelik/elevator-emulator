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
      floors: [
        {
          number: 1,
          isActiveButton: false,
          isSelectElevator: false,
        },
        {
          number: 2,
          isActiveButton: false,
          isSelectElevator: false,
        },
        {
          number: 3,
          isActiveButton: false,
          isSelectElevator: false,
        },
      ],
      elevators: [
        {
          id: 1,
          startFloorNumber: 1,
          endFloorNumber: 1,
          speed: 1000,
          waitTime: 3000,
          status: "free",
        },
        {
          id: 2,
          startFloorNumber: 1,
          endFloorNumber: 1,
          speed: 1000,
          waitTime: 3000,
          status: "free",
        },
      ],
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
  },
  methods: {
    handlerCallOfElevator(floorNumber) {
      if (!this.isElevatorOnTargetFloor(floorNumber)) {
        this.floors[floorNumber - 1].isActiveButton = true;
        this.callQueue = [...this.callQueue, floorNumber];
      }
    },
    isElevatorOnTargetFloor(floorNumber) {
      return this.freeElevators.find(
        (elevator) => elevator.startFloorNumber === floorNumber
      );
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
