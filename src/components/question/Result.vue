<template>
<div id="Result">
  <div class="container">
    <h1>{{exam.title}}</h1>
    <h2>{{exam.year}}</h2>
    <h3>共{{total}}题 你答对了{{totalCorrect}}题</h3>
    <h3>{{evaluate}}</h3>
    <hr>
    <div v-for="(question,index) in questions">
      <Row>
        <Col span="4">
        <strong>{{index+1}}.</strong>
        <p>
          <Button size="large" type="text" @click="addColl(index)"><Icon type="ios-star-outline"></Icon></Button>
        </p>
        <!-- <p>
          <Button size="small" type="ghost">题目报错</Button>
        </p> -->
        </Col>
        <Col span="14" align='left'>
        <div>
          <span v-html="_expr(question.content)"></span>
        </div>
        <div>
          <ul class="options">
            <li v-for="(option,indexO) in question.options">
              {{ABCD[indexO]}}. <span v-html="_expr(question.options[indexO])"></span>
            </li>
          </ul>
        </div>
        </Col>
      </Row>
      <Row class="explain">
        <Col span="8" offset="4" align="left">
        <p v-bind:class="{correct: resultTF[index], wrong: !resultTF[index]}">你的选择:{{showSelectABCD(index)}}</p>
        <p>正确答案:{{showCorrectABCD(index)}}</p>
        <!-- <Collapse>
          <Panel name="1">
            查看解析：
            <p slot="content">{{questionAnswers[index].explain}}</p>
          </Panel>
        </Collapse> -->
        </Col>
      </Row>
    </div>
    <div class="footer">
      <Button @click="finish" size="large" type="info">完成</Button>
    </div>
  </div>
</div>
</template>

<script>
import axios from '@/axios';
export default {
  name: "Result",
  data: () => ({
    ABCD: ["A", "B", "C", "D"]
  }),

  methods: {
    /**
     * Katex transforn
     * @param  {string} expr input string
     * @return {string}      ouput string
     */
    _expr: function (expr) {
      var _expr = expr.replace(/\s\s+/g, ' ').replace(/ /gi, "~");
      try {
        return katex.renderToString(_expr, {
          throwOnError: false
        });
      } catch (err) {
        return expr
      }
    },

    /**
     * Transform user select options from number to ABCD
     * @param  {number} index array index
     * @return {string}       A,B,C,D
     */
    showSelectABCD(index) {
      let option = this.selected[index];
      return this.showABCD(option);
    },

    showCorrectABCD(index) {
      let answer = this.answers[index];
      let option = answer.correct;
      return this.showABCD(option);
    },

    /**
     * Transform uitl
     * @param  {number} option 0,1,2,3
     * @return {string}        A,B,C,D
     */
    showABCD(option) {
      //   option = option.toString();
      switch (option) {
        case "0":
          return "A";
          break;
        case 0:
          return "A";
          break;
        case "1":
          return "B";
          break;
        case 1:
          return "B";
          break;
        case "2":
          return "C";
          break;
        case 2:
          return "C";
          break;
        case "3":
          return "D";
          break;
        case 3:
          return "D";
          break;
        default:
          return "error"

      }
    },

    finish() {
      this.$store.dispatch('clear');
      this.$router.push('/exam/normal');
    },

    addColl(index) {
      let questionId = this.questions[index].id;
      let subjectId = 5; //temp for test
      let name = this.$store.state.user.name;
      let note = "this is note";
      let pack = {
        ProblemId: questionId,
        courseType: subjectId,
        username: name,
        note: note
      };
      axios({
        method: 'post',
        url: '/course/record/',
        data: pack
      }).then(response => {
        let success = response.data.success;
        if (success) {
          this.$Message.success('添加收藏成功!');
        } else {
          this.$Message.error('添加收藏失败!');
        }
      }).catch(e => {
        console.log(e);
      });
    }
  },

  computed: {
    exam() {
      return this.$store.state.course.exam;
    },
    selected() {
      return this.$store.state.course.selected;
    },
    questions() {
      return this.$store.state.course.questions;
    },
    total() {
      return this.$store.getters.total;
    },
    answers() {
      return this.$store.state.course.answers;
    },
    result() {
      return this.$store.state.course.result;
    },
    evaluate() {
      return this.$store.state.course.evaluate;
    },
    totalCorrect() {
      return this.$store.getters.totalCorrect;
    },
    resultTF() {
      return this.result.map((r) => {
        return Number(r);
      })
    }
  }
}
</script>

<style lang="css" scoped>
.options > *{
    margin: 40px;
    line-height: 2;
}

.explain{
    margin-bottom: 40px;
}

.correct{
    color: green;
}

.wrong{
    color: red;
}

.footer{
    margin:50px;
}
</style>
