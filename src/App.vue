<template>
    <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-12 col-md-12">
                <h1 class="text-center">Vocabulary analisys</h1>
                <textarea v-model="text" class="form-control" rows=10>
                
              </textarea>
              <br>
              <button class="btn" @click="execute('analise')">
                <span v-if="loading==false">
                    Analise
                </span>
                <span v-else="loading==true">
                    Loading...
                </span>
              </button>
              <hr>
              <ul>
                <li>
                    sentences: {{sentences}}
                </li>
                <li>
                    words: {{words}}
                </li>
                <li>
                    syllables: {{syllables}}
                </li>
              </ul>
            </div>
        </div>
        <hr>
        <div class="row">
            <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
                <transition name="flip" mode="out-in">
                    <component :is="mode" @answered="answered($event)" @confirmed="mode = 'app-question'"></component>
                </transition>
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
                sentences: 0,
                words: 0,
                syllables: 0,
                wordSeparators: [' ',',','.','!','?','"','-'],
                sentenceSeparators: ['.','!','?'],
                vowels: ['а', 'ә', 'о', 'е', 'ы', 'і', 'о', 'ө', 'ұ', 'ү', 
                         'А', 'Ә', 'О', 'Е', 'Ы', 'І', 'О', 'Ө', 'Ұ', 'Ү'],
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
                var w = 0;
                var s = 0;
                var syl = 0;
                //for(var j=0;j<1000;j++){
                    if(this.vowels.indexOf(t[0])!==-1) syl++;
                    for(var i=1;i<t.length-1;i++){
                        if(this.isSeparator(t[i-1],t[i],t[i+1],"word")){
                            w++;
                        }
                        if(this.isSeparator(t[i-1],t[i],t[i+1],"sentence")){
                            s++;
                        }
                        if(this.vowels.indexOf(t[i])!==-1)
                            syl++;
                    }
                    if(this.vowels.indexOf(t[t.length-1])!==-1) syl++;
                    if(this.wordSeparators.indexOf(t[t.length-1])===-1) w++;
                    if(this.sentenceSeparators.indexOf(t[t.length-1])===-1) s++;
                //}
                this.words = w;
                this.sentences = s;
                this.syllables = syl;
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
        },
    }
</script>

<style>
</style>
