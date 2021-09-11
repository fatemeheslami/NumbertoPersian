<template>
  <div>
    <div class="my-5">
      <input
        id="tooltip-target-1"
        type="text"
        class="input text-center mt-3 ml-5 w-25"
        @keyup="show"
      />
      <span class="mt-3 ml-5 pl-2">ریال</span>
    </div>

    <b-tooltip target="tooltip-target-1" triggers="hover">
      {{ title }}
    </b-tooltip>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: '',

      letters: [
        ['', 'یک ', 'دو', 'سه', 'چهار', 'پنج', 'شش', 'هفت', 'هشت', 'نه'],
        [
          'ده',
          'یازده',
          'دوازده',
          'سیزده',
          'چهارده',
          'پانزده',
          'شانزده',
          'هفده',
          'هجده',
          'نوزده',
          ' بیست ',
        ],
        [
          '',
          '',
          ' بیست ',
          ' سی ',
          ' چهل ',
          ' پنجاه ',
          ' شصت ',
          ' هفتاد ',
          ' هشتاد ',
          ' نود ',
        ],
        [
          '',
          ' یکصد ',
          ' دویست ',
          ' سیصد ',
          ' چهارصد ',
          ' پانصد ',
          ' ششصد ',
          ' هفتصد ',
          ' هشتصد ',
          ' نهصد ',
        ],
        ['', '  هزار  ', ' میلیون  ', ' میلیارد '],
      ],
    }
  },

  methods: {
    show(e) {
      if (e.target.value.length < 17) {
        e.target.value = Number(
          e.target.value.replace(/,/g, '')
        ).toLocaleString()
      } else {
        e.target.value = ''
        alert('مبلغ وارد شده بیش از حد مجاز است')
      }

      this.numToPersian(e.target.value)
    },
    prepareNum(num) {
      if (num.length % 3 === 1) {
        num = `00${num}`
      } else if (num.length % 3 === 2) {
        num = `0${num}`
      }

      return num.replace(/\d{3}(?=\d)/g, '$&*').split('*')
    },
    numToWord(num) {
      const delimiter = ' و '

      if (parseInt(num, 0) === 0) {
        return ''
      }
      const parsedInt = parseInt(num, 0)

      if (isNaN(parsedInt) === true) {
        return ' '
      }

      if (parsedInt < 10) {
        return this.letters[0][parsedInt]
      }
      if (parsedInt <= 20) {
        return this.letters[1][parsedInt - 10]
      }

      if (parsedInt < 100) {
        const one = parsedInt % 10
        const ten = (parsedInt - one) / 10
        if (one > 0) {
          return this.letters[2][ten] + delimiter + this.letters[0][one]
        }

        return this.letters[2][ten]
      }

      const one = parsedInt % 10
      const hundred = (parsedInt - (parsedInt % 100)) / 100
      const ten = (parsedInt - (hundred * 100 + one)) / 10
      const out = [this.letters[3][hundred]]
      const secondPart = ten * 10 + one
      if (secondPart === 0) {
        return out
      }

      if (secondPart < 10) {
        out.push(this.letters[0][secondPart])
      } else if (secondPart <= 20) {
        out.push(this.letters[1][secondPart - 10])
      } else {
        out.push(this.letters[2][ten])
        if (one > 0) {
          out.push(this.letters[0][one])
        }
      }

      return out.join(delimiter)
    },

    numToPersian(out) {
      out = out.substring(0, out.length - 1).replace(/[^0-9.-]/g, '')

      const sliceNum = this.prepareNum(out)
      // console.log(sliceNum)

      const output = []

      for (let i = 0; i < sliceNum.length; i++) {
        const convertNum = this.numToWord(sliceNum[i])

        if (convertNum !== '') {
          output.push(convertNum + this.letters[4][sliceNum.length - (i + 1)])

          this.title = output.toString().replace(/,/gi, ' و ') + ' تومان '
        }
      }
    },
  },
}
</script>

<style>
span {
  position: absolute;
  left: 2px;
}
</style>