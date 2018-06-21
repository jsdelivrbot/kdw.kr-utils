<template>
  <div id="main">
    <div :class="$style['encode']">
      <h3>인코딩/디코딩</h3>
      <div :class="$style['option']">
        <select :class="$style['encryptTypeSelect']" v-model="selectedEncryptType">
          <option v-for="(encrypt, key) in encryptList" :value="key">{{encrypt.label}}</option>
        </select>
        <input :class="$style['encryptKey']" v-model="encryptKey" placeholder="암호키" :disabled="!needEncryptKey">
      </div>
      <div :class="$style['textbox']">
        <textarea v-model="decryptText" placeholder="인코딩할 텍스트"></textarea>
        <textarea v-model="encryptText" placeholder="디코딩할 텍스트"></textarea>
      </div>
    </div>
    <div :class="$style['numberofletters']">
      <h3>글자수 세기</h3>
      <textarea v-model="textlength"></textarea>
      <div>
        {{textlength.length}} 글자(띄어쓰기 포함)<br>
        {{textlength.replace(/ /g,'').length}} 글자(띄어쓰기 미포함)
      </div>
    </div>
    <div :class="$style['numberofletters']">
      <h3>글자 뒤집기</h3>
      <textarea v-model="textreverse"></textarea>
      <textarea readonly>{{textreversed}}</textarea>
    </div>
    <div :class="$style['transfertext']">
      <h3>글자 변환</h3>
      <textarea v-model="transferText.text" placeholder="원본"/>
      <textarea v-model="transferText.ascii" placeholder="아스키코드"/>
      <textarea v-model="transferText.binary" placeholder="2진수"/>
      <textarea v-model="transferText.octal" placeholder="8진수"/>
      <textarea v-model="transferText.decimal" placeholder="10진수"/>
      <textarea v-model="transferText.hex" placeholder="16진수"/>
    </div>
  </div>
</template>

<script>
const CryptoJS = require("crypto-js")
export default {
  data () {
    return {
      encryptList: [
        {label: 'URL 인코딩', function: {encrypt: encodeURI, decrypt: decodeURI}},
        {label: 'Base64', function: {encrypt: b64EncodeUnicode, decrypt: b64DecodeUnicode}},
        {label: 'MD5', function: {encrypt: CryptoJS.MD5}},
        {label: 'SHA1', function: {encrypt: CryptoJS.SHA1}},
        {label: 'SHA256', function: {encrypt: CryptoJS.SHA256}},
        {label: 'SHA224', function: {encrypt: CryptoJS.SHA224}},
        {label: 'SHA512', function: {encrypt: CryptoJS.SHA512}},
        {label: 'SHA384', function:{encrypt:  CryptoJS.SHA384}},
        {label: 'SHA3', function: {encrypt: CryptoJS.SHA3}},
        {label: 'RIPEMD160', function: {encrypt: CryptoJS.RIPEMD160}},
        {label: 'AES', function: CryptoJS.AES, needKey: true},
        {label: 'TripleDES', function: CryptoJS.TripleDES, needKey: true},
        {label: 'RC4', function: CryptoJS.RC4, needKey: true},
        {label: 'Rabbit', function: CryptoJS.Rabbit, needKey: true},
        {label: '시저 암호(암호키에 숫자 입력)', function: {encrypt: caesar, decrypt: caesar}, needKey: true},
      ],
      selectedEncryptType: 0,
      selectedEncrypt: {},
      encryptKey: '',
      needEncryptKey: false,
      encryptText: '',
      decryptText: '',
      chkEncrypt: false,

      textlength: '',
      textreverse: '',
      textreversed: '',

      transferText: {
        text: '',
        ascii: '',
        binary: '',
        octal: '',
        decimal: '',
        hex: '',
      }
    }
  },
  mounted () {
    this.selectedEncrypt = this.encryptList[0]
    this.needEncryptKey = this.selectedEncrypt.needKey
  },
  computed: {
    transferText_ () {
      return this.transferText.text
    }
  },
  watch: {
    selectedEncryptType () {
      this.selectedEncrypt = this.encryptList[this.selectedEncryptType]
      this.needEncryptKey = this.selectedEncrypt.needKey
    },
    decryptText () {
      this.chkEncrypt = true
      const func = this.selectedEncrypt.function
      this.encryptText = this.needEncryptKey?func.encrypt(this.decryptText, this.encryptKey).toString():func.encrypt(this.decryptText)
    },
    encryptText () {
      const func = this.selectedEncrypt.function
      if (this.chkEncrypt) this.chkencrypt = false
      else this.decryptText = this.needEncryptKey?func.encrypt(this.encryptText, this.encryptKey).toString(CryptoJS.enc.Utf8):func.encrypt(this.encryptText)
    },
    textreverse () {
      this.textreversed = reverseString(this.textreverse)
    },
    transferText_ () {
      var a=[], txt = this.transferText_
      for(var i=0;i<txt.length;i++) a.push(txt.charCodeAt(i))
      this.transferText.ascii = a.join(' ')
      a=[]
      for(var i=0;i<txt.length;i++) a.push(txt.charCodeAt(i).toString(2))
      this.transferText.binary = a.join(' ')
      a=[]
      for(var i=0;i<txt.length;i++) a.push(txt.charCodeAt(i).toString(8))
      this.transferText.octal = a.join(' ')
      a=[]
      for(var i=0;i<txt.length;i++) a.push(txt.charCodeAt(i).toString(10))
      this.transferText.decimal = a.join(' ')
      a=[]
      for(var i=0;i<txt.length;i++) a.push(txt.charCodeAt(i).toString(16))
      this.transferText.hex = a.join(' ')
    }
  }
}

function reverseString(str) {
    return str.split("").reverse().join("")
}

function caesar(s, n) {
  let arr = [];
  n = n % 26
  for (let i = 0; i < s.length; i++) {
    const word = s.charCodeAt(i)
    if (word == 32) {
      arr.push(" ")
    } else {
      if (word >= 65 && word <= 90) {
        if (word + n > 90) {
          arr.push(String.fromCharCode(word + n - 26))
        } else {
          arr.push(String.fromCharCode(word + n))
        }
      } else if (word >= 90 && word <= 122) {
        if (word + n > 122) {
          arr.push(String.fromCharCode(word + n - 26))
        } else {
          arr.push(String.fromCharCode(word + n))
        }
      }
    }
  }
  return arr.join("")
}

function b64EncodeUnicode(str) {
    return btoa(encodeURIComponent(str).replace(/%([0-9A-F]{2})/g,
        function toSolidBytes(match, p1) {
            return String.fromCharCode('0x' + p1)
    }))
}
function b64DecodeUnicode(str) {
    return decodeURIComponent(atob(str).split('').map(function(c) {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
    }).join(''))
}
</script>

<style lang="scss" module>
.encode {
  margin: 1rem;
  .textbox {
    margin-top: 1rem;
    textarea {
      height: 10rem;
    }
  }
}
.numberofletters {
  margin: 1rem;
  textarea {
    height: 10rem;
    width: 30rem;
  }
  div {
    display: inline-block;
  }
}
.transfertext {
  margin: 1rem;
  textarea {
    height: 10rem;
  }
}
</style>
<style lang="scss" scoped>
input, textarea, select {
  border: 1px solid #ccc;
  width: 20rem;
  border-radius: 0.25rem;
  padding: 0.25rem;
  font: inherit;
}
</style>
