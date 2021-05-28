<script>
import { Line } from "vue3-chart-v2";
import moment from "moment";

export default {
  props: ["hourly"],
  extends: Line,
  mounted() {
    const hours = this.hourly.slice(0, 24);
    let labels = [];
    let tempData = [];
    hours.forEach((hour) => {
      const time = moment.unix(hour.dt).format("HH:mm");
      labels.push(time);
      tempData.push(hour.temp);
    });
    this.renderChart(
      // Data
      {
        labels,
        datasets: [
          {
            label: "Temperature",
            backgroundColor: "transparent",
            borderColor: "#f87979",
            data: tempData,
          },
        ],
      },
      // Options
      {
        maintainAspectRatio: false,
      }
    );
  },
};
</script>
