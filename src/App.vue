<template>
    <div class="container">
        <br>
        <div class="row">
            <div class="col-xs-12 col-sm-12 col-md-12 col-lg-8">
                <div class="card">
                  <div class="card-header">
                    <h3 class="float-left">Vocabulary analisys</h3>
                    <button class="btn float-right" @click="execute('analise')">
                        <span v-if="loading==false">
                            Analise
                        </span>
                        <span v-else="loading==true">
                            Loading...
                        </span>
                    </button>
                  </div>
                  <div class="card-body">
                    <textarea v-model="text" class="form-control" rows=10>
                        
                    </textarea>
                  </div>
                </div>
            </div>
            <br>
            <div class="col-xs-12 col-sm-12 col-md-12 col-lg-4">
                <div class="card">
                    <ul class="list-group list-group-flush">
                        <li class="list-group-item">
                            Character count: {{characters}}
                        </li>
                        <li class="list-group-item">
                            Syllable count: {{syllables}}
                        </li>
                        <li class="list-group-item">
                            Word count: {{words}}
                        </li>
                        <li class="list-group-item">
                            Sentence count: {{sentences}}
                        </li>
                        <li class="list-group-item">
                            Average syllables per word: {{syllablesPerWord}}
                        </li>
                        <li class="list-group-item">
                            Average words per sentence: {{wordsPerSentence}}
                        </li>
                        <li class="list-group-item">
                            Max syllables per word: {{maxSyllablesPerWord}}
                        </li>
                        <li class="list-group-item">
                            Max words per sentence: {{maxWordsPerSentence}}
                        </li>
                        <li class="list-group-item">
                            Readability: {{readability}} %
                        </li>
                        <li class="list-group-item">
                            Grade level: {{grade}}
                        </li>
                        <li class="list-group-item">
                            Reading time: {{digitize(reading)}}
                        </li>
                        <li class="list-group-item">
                            Speaking time: {{digitize(speaking)}}
                        </li>
                    </ul>
                </div>
                
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        data() {
            return {
                loading: false,
                text: `Күн бата ауылдан шықтық. Біздің де жеккеніміз пар ат. Делбені өзім ұстадым. Қатты шыққан қарқынымызбен келіп қалдық. Әбдірахманның екі көзі алдында. Алдымызда жүз метрдей жерде Есімбектің үйі тұр,бірақ Шұға көрінбейді. Енді кішкене жүрсек,өтіп те кетеміз,бірақ өткіміз келмейді. Аттың басын тежей беремін. Бұдан кетсе, Әбдірахман Шұғаны мәңгі көре алмас деген ой елестейді. Бір-бірімізбен сөйлеспей келеміз, сонда да біріміздің ойлағанымызды екіншіміз біліп келеміз. Есімбектің қабаған төбеттері алдымыздан шықты. Есіктің алдында бірталай адам жағалай отыр, бірақ бәрінің көзі бізде.  Басында кимешек киген Шұғаның үлкен жеңгесі түйе сауып тұр. Үлкен үй мен отаудың арасында бәйбіше теңселіп жүр. Осының бәрі маған қуаныш секілді көрінді, сонда да біздің кетіп бара жатқанымызға ешкім таңырқаған жоқ. Ат тоқтауға айналған екен,сөйтсе де, делбені жиыңқырап ұстай бергенімде,ширыға жөнелді. Осы кезде ауылдың сырт жағындағы құдықтан келе жатқан екі әйел көрінді. Біреуі-Шұға. Әбдірахман арбадан қарғып түсті.`,
                characters: 0,
                sentences: 0,
                words: 0,
                syllables: 0,
                wordSeparators: [' ',',','.','!','?','"','-'],
                sentenceSeparators: ['.','!','?'],
                vowels: ['а', 'ә', 'о', 'е', 'ы', 'і', 'о', 'ө', 'ұ', 'ү', 
                         'А', 'Ә', 'О', 'Е', 'Ы', 'І', 'О', 'Ө', 'Ұ', 'Ү'],
                maxSyllablesPerWord:-1,
                maxWordsPerSentence:-1,
                /*vocabulary: [
                    {
                        word: 'абаған',
                        partOfSpeech: 'Зат есім',
                        styles: [1,2,3],
                        occurences: 2,
                        rank: 0.00003,
                    }
                ],
                styles:[
                    {index:1,name:"Ғылыми"},
                    {index:2,name:"Іскери"},
                    {index:3,name:"Көркем"},
                    {index:4,name:"Публицистика"},
                    {index:5,name:"Сөйлеу"},
                    {index:6,name:"Ортақ база"},
                ],*/
            }
        },
        created(){
            this.execute('analise');
        },
        methods: {
            execute(name){
                this.loading = true;
                if(name==='analise')
                    setTimeout(() => {
                      var result = this.analise();
                    }, 10);
            },
            analise(){
                this.text = this.text.trim();
                var t = this.text;
                var c = 0;
                var w = 0;
                var s = 0;
                var syl = 0;
                var spw = 0;
                var wps = 0;
                this.maxSyllablesPerWord = 0;
                this.maxWordsPerSentence = 0;
                //for(var j=0;j<1000;j++){
                    if(this.vowels.indexOf(t[0])!==-1){ 
                        syl++;
                        spw++;
                    }
                    if(this.wordSeparators.indexOf(t[0])===-1) c++;
                    for(var i=1;i<t.length-1;i++){
                        if(this.isSeparator(t[i-1],t[i],t[i+1],"word")){
                            w++;
                            if(this.maxSyllablesPerWord<spw)
                                this.maxSyllablesPerWord=spw
                            spw=0;
                            wps++;
                        }
                        if(this.isSeparator(t[i-1],t[i],t[i+1],"sentence")){
                            s++;
                            if(this.maxWordsPerSentence<wps)
                                this.maxWordsPerSentence=wps
                            wps=0;
                        }
                        if(this.vowels.indexOf(t[i])!==-1){
                            syl++;
                            spw++;
                        }
                        if(this.wordSeparators.indexOf(t[i])===-1){
                            c++;
                        }
                    }
                    if(this.vowels.indexOf(t[t.length-1])!==-1){
                        syl++;
                        spw++;
                        if(this.maxSyllablesPerWord<spw)
                            this.maxSyllablesPerWord=spw
                    }
                    if(this.wordSeparators.indexOf(t[t.length-1])===-1){
                        w++;
                        wps++;
                        if(this.maxSyllablesPerWord<spw)
                            this.maxSyllablesPerWord=spw
                        spw=0;
                        c++;
                    }
                    if(this.sentenceSeparators.indexOf(t[t.length-1])===-1){
                        s++;
                        if(this.maxWordsPerSentence<wps)
                            this.maxWordsPerSentence=wps
                    } 
                //}
                this.maxSyllablesPerWord--;
                this.words = w;
                this.sentences = s;
                this.syllables = syl;
                this.characters = c;
                this.loading = false;
            },
            isSeparator: function(x,y,z,separatorFor){
                if(separatorFor==='word'){
                    if(
                        this.wordSeparators.indexOf(x)===-1 &&
                        this.wordSeparators.indexOf(y)===-1 &&
                        this.wordSeparators.indexOf(z)!==-1
                    )
                        return true;
                    else
                        return false;
                }else if(separatorFor==='sentence'){
                    if(
                        this.sentenceSeparators.indexOf(x)===-1 && 
                        this.sentenceSeparators.indexOf(y)===-1 && 
                        this.sentenceSeparators.indexOf(z)!==-1
                    )
                        return true;
                    else
                        return false;
                }else{
                    return false;
                }
            },
            digitize(totalSeconds){
                var minutes = Math.round(totalSeconds/60)
                var hours = 0
                if(minutes>60){
                    hours = Math.round(minutes/60)
                    minutes = minutes%60
                }
                var seconds = totalSeconds%60
                var mm = minutes<10 ?"0"+minutes:minutes
                var ss = seconds<10 ?"0"+seconds:seconds
                var hh = hours<10?"0"+hours:hours
                return hh+":"+mm+":"+ss
            }
        },
        computed: {
            syllablesPerWord(){
                if(this.words!==0)
                    return Math.round(this.syllables*100/this.words)/100;
                else
                    return 0;
            },
            wordsPerSentence(){
                if(this.sentences!==0)
                    return Math.round(this.words*100/this.sentences)/100;
                else
                    return 0;
            },
            readability(){
                //Flesch–Kincaid readability tests
                if(this.words!==0 && this.sentences!==0){
                    return Math.round((280 - 1.015*this.wordsPerSentence - 84.6*this.syllablesPerWord)*100)/100;
                }else{
                    return 0;
                }
            },
            grade(){
                if(this.readability!==0){
                    if(100 < this.readability)
                        return "1 - 4th";
                    else if(100 >= this.readability && this.readability > 90){
                        return "5th";
                    }else if(90 >= this.readability && this.readability > 80){
                        return "6th";
                    }else if(80 >= this.readability && this.readability > 70){
                        return "7th";
                    }else if(70 >= this.readability && this.readability > 60){
                        return "8th and 9th";
                    }else if(60 >= this.readability && this.readability > 50){
                        return "10th and 12th";
                    }else if(50 >= this.readability && this.readability > 30){
                        return "College";
                    }else{
                        return "College graduate";
                    }
                }else{
                    return "";
                }
            },
            reading(){
                //http://www.assafelovic.com/blog/2017/6/27/estimating-an-articles-reading-time
                var wpm = 200
                var word_length = 5
                var total_words = 0
                var total_words = this.text.length/word_length
                return Math.round(total_words*60/wpm)
                //return this.digitizeTime(totalSeconds)
            },
            speaking(){
                //http://www.speechinminutes.com
                if(this.words>0){
                    var wpm = 130
                    return Math.round(this.words*60/wpm)
                }else
                    return 0                
            }
        },
    }
</script>

<style>
</style>
