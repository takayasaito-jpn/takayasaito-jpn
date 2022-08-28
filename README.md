<!DOCTYPE html>
<html>
 
<head>
    <meta charset='sjis'>
    <title>BMI計測</title>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
<script>
/**
 * 任意の桁で四捨五入する関数
 * @param {number} value 四捨五入する数値
 * @param {number} base どの桁で四捨五入するか（10→10の位、0.1→小数第１位）
 * @return {number} 四捨五入した値
 */
function orgRound(value, base) {
    return Math.round(value * base) / base;
}

let height; //変数 身長
let bodyfat; //変数 体脂肪量
let leanbodymass; //変数 体脂肪量
 
 
 
//BMI計算（除脂肪量 + 脂肪量）÷ 身長の2乗
function calc_bmi() {
 
    weight = document.getElementById('weight').value; //体重の値を取得
    height = document.getElementById('height').value; //身長の値を取得
 
    //入力した値が0以上の場合計算処理する
    if (parseFloat(weight) > 0 && parseFloat(height) > 0) {  //parseFloat(文字列変数)⇒数値に変換
 
        //身長をcm から m へ変換
        let height_m = getMeterHeight();
        
        //BMI計算
        let bmi = weight / (height_m * height_m);
 
        //計算式表示
        document.getElementById("bmi_weight_value").innerHTML = weight;
        document.getElementById("bmi_height_value").innerHTML = height;
 
        //BMI結果表示
        document.getElementById("bmi_value").innerHTML = orgRound(bmi,10);  //小数点桁数指定で四捨五入の場合、桁数をずらしてあげる. 

        calc_bodyfat();

    }
    //入力された値が0以上でない場合エラー
    else {
        //skip
    }
}

function getMeterHeight(){
        height = document.getElementById('height').value; //身長の値を取得

        //身長をcm から m へ変換
        let height_m = height / 100;
        
        return height_m;
}

//脂肪量計算
function calc_bodyfat() {
 
    weight = document.getElementById('weight').value;
    bodyfat_per = document.getElementById('bodyfat_per').value;
 
    if (parseFloat(weight) > 0 && parseFloat(height) > 0) {
 
        //身長をcm から m へ変換
        let height_m = getMeterHeight();
        bodyfat = orgRound( weight * (bodyfat_per / 100), 100);
        leanbodymass =  weight - bodyfat;
  
        document.getElementById("bodyfat").value = bodyfat;
        document.getElementById("lean_body_mass").value = leanbodymass;
        
        calc_fmi();
        calc_ffmi();
        calc_BMR_haris();
        calc_BMR_mif();
        calc_BMR_national();
        calc_BMR_sports();
        
        if(getOpVal("pfc_type")=="normal"){
          calc_pfc(0.17,0.23,0.6);
        }else if(getOpVal("pfc_type")=="lowfat"){
          calc_pfc(0.3,0.1,0.6);
        }else{
          calc_pfc(0.3,0.6,0.1);
        }
        calc_pfc_g();
        
    }
    //入力された値が0以上でない場合エラー
    else {
        //skip
    }
     
}
//FMI計算 脂肪量 ÷ 身長の2乗
function calc_fmi() {
 
    height = document.getElementById('height').value;
    bodyfat = document.getElementById('bodyfat').value;
 
    if (parseFloat(weight) > 0 && parseFloat(height) > 0) {
 
        //身長をcm から m へ変換
        let height_m = getMeterHeight();
        document.getElementById("fmi").value = orgRound(bodyfat / (height_m * height_m), 100);
        
    }
    //入力された値が0以上でない場合エラー
    else {
        //skip
    }
     
}
//FFMI計算 体重[kg] x (1 - 体脂肪率) ÷ (身長[m])2
function calc_ffmi() {
  
    weight = document.getElementById('weight').value;
    height = document.getElementById('height').value;
    bodyfat_per = document.getElementById('bodyfat_per').value;
 
    if (parseFloat(weight) > 0 && parseFloat(height) > 0) {
 
        //身長をcm から m へ変換
        let height_m = getMeterHeight();
  
        //FMI結果表示
        document.getElementById("ffmi").value = orgRound(weight * (1 - bodyfat_per / 100) / (height_m * height_m), 100);
        
    } else {
        //skip
    }
}
//基礎代謝ハリスベネディクト(改良版) Basal metabolic rate
function calc_BMR_haris() {
    age = document.getElementById('age').value;
    weight = document.getElementById('weight').value;
    height = document.getElementById('height').value;
    amv = parseFloat( getActivityMetabolismVal() );
    
    if( getOpVal("sex")=="man" ){
      document.getElementById("bmr_haris").value = orgRound( 13.397 * weight + (4.799 * height) - (5.677 * age) + 88.362 , 100);
      document.getElementById("tdee_haris").value = orgRound((13.397 * weight + (4.799 * height) - (5.677 * age) + 88.362) * amv , 100);
    }else{
      document.getElementById("bmr_haris").value = orgRound(9.247 * weight + (3.098 * height) - (4.33 * age) + 447.593, 100);
      document.getElementById("tdee_haris").value = orgRound((9.247 * weight + (3.098 * height) - (4.33 * age) + 447.593) * amv, 100);
    }
    
}

//基礎代謝ミフリンセイントジョー Basal metabolic rate man: 10×体重(kg)＋6.25×身長(cm)ー5×年齢＋5
function calc_BMR_mif() {
    age = document.getElementById('age').value;
    weight = document.getElementById('weight').value;
    height = document.getElementById('height').value;
    amv = parseFloat( getActivityMetabolismVal() );

    if( getOpVal("sex")=="man" ){
      document.getElementById("bmr_mif").value =  orgRound( 10 * weight + 6.25 * height - 5 * age + 5, 100);
      document.getElementById("tdee_mif").value =  orgRound( (10 * weight + 6.25 * height - 5 * age + 5) * amv, 100);
    }else{
      document.getElementById("bmr_mif").value =  orgRound( 10 * weight + 6.25 * height - 5 * age - 161, 100);
      document.getElementById("tdee_mif").value =  orgRound( (10 * weight + 6.25 * height - 5 * age - 161) * amv, 100);
    }
    
}

//基礎代謝国立健康・栄養研究所式 Basal metabolic rate
function calc_BMR_national() {
    age = document.getElementById('age').value;
    weight = document.getElementById('weight').value;
    height = document.getElementById('height').value;
    amv = parseFloat( getActivityMetabolismVal() );

    if( getOpVal("sex")=="man" ){
      document.getElementById("bmr_national").value = orgRound( (0.0481 * weight + 0.0234 * height - 0.0138 * age - 0.4235 ) * 1000/4.186, 100);
      document.getElementById("tdee_national").value = orgRound( ((0.0481 * weight + 0.0234 * height - 0.0138 * age - 0.4235 ) * 1000/4.186) * amv, 100);
    }else{
      document.getElementById("bmr_national").value = orgRound( (0.0481 * weight + 0.0234 * height - 0.0138 * age - 0.9708 ) * 1000/4.186, 100);
      document.getElementById("tdee_national").value = orgRound( ((0.0481 * weight + 0.0234 * height - 0.0138 * age - 0.9708 ) * 1000/4.186) * amv, 100);
    }    
}

//基礎代謝 国立スポーツ科学センター式 Basal metabolic rate
function calc_BMR_sports() {
    lean_body_mass = document.getElementById('lean_body_mass').value;
    document.getElementById("bmr_sports").value = 28.5 * lean_body_mass;
}

function calc_pfc(p,f,c) {
    bmr = document.getElementById('bmr_haris').value;
    pfc_p = orgRound( bmr * p, 10);
    pfc_f = orgRound( bmr * f, 10);
    pfc_c = orgRound( bmr * c, 10);
    
    document.getElementById("pfc_p").value = pfc_p;
    document.getElementById("pfc_f").value = pfc_f;
    document.getElementById("pfc_c").value = pfc_c;
    document.getElementById("pfc_cal").value = orgRound(pfc_p + pfc_f + pfc_c, 10);
}

function calc_pfc_g(p,f,c) {
  
    pfc_p_g = orgRound( document.getElementById("pfc_p").value / 4, 10);
    pfc_f_g = orgRound( document.getElementById("pfc_f").value / 9, 10);
    pfc_c_g = orgRound( document.getElementById("pfc_c").value / 4, 10);
    
    document.getElementById("pfc_p_g").innerHTML = pfc_p_g+"g";
    document.getElementById("pfc_f_g").innerHTML = pfc_f_g+"g";
    document.getElementById("pfc_c_g").innerHTML = pfc_c_g+"g";
}

//活動レベルSelect取得
function getActivityMetabolismVal() {
    let e = document.getElementById('activity_metabolism');

    return e.selectedOptions[0].value;
}

//ラジオ取得
function getOpVal(name) {
    let e = document.getElementsByName(name);
    let len = e.length;
    let checkValue = '';

    for (let i = 0; i < len; i++){
        if (e.item(i).checked){
            checkValue = e.item(i).value;
        }
    }
    return checkValue;
}

</script>

<body>
  <form action="送信先のURL">

  <p>筋トレが好きです。筋トレは意外に化学です。<br>
  でもほしい計算がない。トレーニーは計算に弱いのです。<br>無いなら自分で作るしかない、これも一種のバルクアップ！ということで公開してます。<br>
  ご自身の判断でご自由にご利用ください。<br>連絡先はこちら。反響あるとうれしい</p>

  <div>
    <label>
      性別
      <input type="radio" name="pfc_type" value="normal" checked>normal
      <input type="radio" name="pfc_type" value="keto">ケトジェニック時
      <input type="radio" name="pfc_type" value="lowfat">ローファット時
    </label>
  </div>
    <div>
      <label>
        性別
        <input type="radio" name="sex" value="man" checked>男性
        <input type="radio" name="sex" value="woman">女性
      </label>
    </div>
    <div>
      <label>
        年齢
        <input type="text" id="age" value="38">
      </label>
    </div>
    <div>
      <label>
        身長(cm)
        <input type="number" id="height" value="174">
      </label>
    </div>
    <div>
      <label>
        体重(kg)
        <input type="number" id="weight" value="80">
      </label>
    </div>
    <div>
      <label>
        体脂肪(%)
        <input type="number" id="bodyfat_per" value="20">
      </label>
    </div>
    <div>
      活動代謝 基礎代謝（ ①の計算式より ）×身体活動レベル（ 下記より ）<br>
      <select name="activity_metabolism" id="activity_metabolism">
        <option value="1.2">×1.2・・・日常生活以外はほぼ運動をしない</option>
        <option value="1.375" selected>×1.375・・・週1～２回ほどウォーキングなどの軽い運動をする</option>
        <option value="1.55">×1.55・・・週３～５回ほど自転車や徒歩通勤片道３０分など日常的に運動する</option>
        <option value="1.725">×1.725・・・キツイと思うレベルの運動を毎日する</option>
        <option value="1.9">×1.9・・・アスリートレベルの運動、一日に何度もきつい運動をほぼ毎日する</option>
      </select>
    </div>
    <br>
    <br>
    <input type="button" id="getResult" value="計算" onclick="calc_bmi()">
    <br>
    <br>
    <div>
      <label>
        体脂肪量(kg)
        <input type="number" id="bodyfat">
      </label>
    </div>
    <br>
    <div>BMI : 
      <label id="bmi_weight_value" >体重</label>
      ÷<label id="bmi_height_value">身長</label><sup>2</sup>
      =<label id="bmi_value">BMI</label>
      <label><br>
     <label>「BMI＝体重(kg) ÷ 身長(m)<sup>2</sup>四捨五入」</label>
    <div>
    </div>
        除脂肪体重(LBM Lean body mass)(kg) 
        <input type="number" id="lean_body_mass">
      </label>
    </div>
    <div>
      <label>
        FMI Fat Mass Index(脂肪組織量指数)
        <input type="number" id="fmi">
      </label>
    </div>
    <div>
      <label>
        FFMI (Fat Free Mass Index)
        <input type="number" id="ffmi">
      </label>
    </div>
    <br>
    <br>
    <div>
      <label>
        基礎代謝計算「ハリス・ベネディクト方程式(改良版)」<br>
        男性: 13.397 × 体重kg + 4.799 x 身長cm - 5.677 × 年齢 + 88.362<br>
        女性: 9.247 × 体重kg + 3.098 x 身長cm - 4.33 × 年齢 + 447.593<br>
        <input type="number" id="bmr_haris">
      </label>
      <label>
        活動代謝
        <input type="number" id="tdee_haris">
      </label>
    </div>
    <div>
      <label>
        基礎代謝計算「ミフリンセイントジョー式」<br>
        男性：10×体重(kg)＋6.25×身長(cm)ー5×年齢＋5<br>
        女性：10×体重(kg)＋6.25×身長(cm)ー5×年齢ー161<br>
        <input type="number" id="bmr_mif">
      </label>
      <label>
        活動代謝
        <input type="number" id="tdee_mif">
      </label>
    </div>
    <div>
      <label>
        基礎代謝計算「国立健康・栄養研究所式」(推奨)<br>
        男性：（0.0481×体重＋0.0234×身長－0.0138×年齢－0.4235）×1000/4.186<br>
        女性：（0.0481×体重＋0.0234×身長－0.0138×年齢－0.9708）×1000/4.186<br>
        <input type="number" id="bmr_national">
      </label>
      <label>
        活動代謝
        <input type="number" id="tdee_national">
      </label>
    </div>
    <div>
      <label>
        基礎代謝計算「国立スポーツ科学センター式」(アスリート向け)<br>
        男性女性共通：28.5x除脂肪体重kg<br>
        <input type="number" id="bmr_sports">
      </label>
    </div>
    <br>
    <br>
    <div>
      <label>
        PFCバランス計算（厚生労働省推奨 P＝13～20％　F＝20～30％　C＝50～65％）<br>
        PFCバランス計算（ケトジェニック）P＝30％　F＝60％　C＝10％）<br>
        PFCバランス計算（ローファット）P＝30％　F＝10％　C＝60％）<br>
        タンパク質Kcal<input type="number" id="pfc_p"> <label id="pfc_p_g" ></label><br>
        脂質Kcal<input type="number" id="pfc_f"> <label id="pfc_f_g" ></label><br>
        炭水化物Kcal<input type="number" id="pfc_c"> <label id="pfc_c_g" ></label><br>
        総Kcal<input type="number" id="pfc_cal">
      </label>
    </div>
    <br> 
	  <table border="1">
	    <tbody>
	      <tr>
	        <th>FMI Women Age</th>
	        <th>25th Percentile (kg/m<sup>2</sup>)</th>
	        <th>75th Percentile (kg/m<sup>2</sup>)</th>
	      </tr>
	      <tr>
	        <td>18 - 34</td>
	        <td>4.6</td>
	        <td>6.6</td
	      ></tr>
	      <tr>
	        <td>35 - 54</td>
	        <td>4.8</td>
	        <td>7.3</td>
	      </tr>
	      <tr>
	        <td>55 - 74</td>
	        <td>6.5</td>
	        <td>10.3</td>
	      </tr>
	      <tr>
	        <td>75+</td>
	        <td>7.5</td>
	        <td>11.4</td>
	      </tr>
	    </tbody>
	  </table>
	  <table border="1">
	    <tbody>
	      <tr>
	        <th>FMI Men Age</th>
	        <th>25th Percentile (kg/m<sup>2</sup>)</th>
	        <th>75th Percentile (kg/m<sup>2</sup>)</th>
	      </tr>
	      <tr>
	        <td>18 - 34</td>
	        <td>3.2</td>
	        <td>5.0</td>
	      </tr>
	      <tr>
	        <td>35 - 54</td>
	        <td>3.7</td>
	        <td>6.0</td>
	      </tr>
	      <tr>
	        <td>55 - 74</td>
	        <td>4.3</td>
	        <td>7.2</td>
	      </tr>
	      <tr>
	        <td>75+</td>
	        <td>5.2</td>
	        <td>7.6</td>
	      </tr>
	    </tbody>
	  </table>
<p>例 1 あなたが 40 歳の男性で、脂肪マス指数が 3.7 であるとします。これにより、25 パーセンタイルになります。
平易な英語に翻訳すると、これは人々の25%が身長に対してあなたよりも脂肪重量が少なく、75%の人々が身長に対してあなたよりも脂肪体重が多いことを意味します。
</p>
<p>例2 あなたが55歳の女性で、脂肪質量指数が10.3
の場合、これはあなたが他の女性の75%よりも身長に対してより多くの脂肪重量を持ち、他の女性の25%があなたよりも身長に対してより多くの脂肪重量を持っていることを意味します。
</p>
   <p>出典：https://drbillsukala.com/fat-mass-index/<br>ライセンス的転載は厳しそう</p>
   
    <table border="1">
      <tbody>
        <tr>
          <td width="72">FFMI</td>
          <td width="110">分類・判定</td>
        </tr>
        <tr>
          <td>～18</td>
          <td>平均以下 	一般レベル</td>
        </tr>
        <tr>
          <td>18～19.5</td>
          <td>平均 	一般レベル</td>
        </tr>
        <tr>
          <td>19.5～21</td>
          <td>平均より多い 中級トレーニー</td>
        </tr>
        <tr><td>21～22.5</td>
          <td>とても多い 中級トレーニー</td>
        </tr>
        <tr><td>22.5～26</td>
          <td>アスリート並 上級トレーニー</td>
        </tr>
        <tr><td>26～28</td>
          <td>ドーピング疑惑 ドラッグ使用の可能性</td>
        </tr>
        <tr><td>28～</td>
          <td>ドーピング確実 ドラッグ使用の可能性</td>
        </tr>
      </tbody>
    </table>
    
    <table border="1">
      <tbody>
        <tr>
          <td>BMI</td>
        <td style="text-align: center;" width="149">分類</td>
        </tr>
        <tr>
          <td>～18.5</td>
          <td>標準体重以下</td>
        </tr>
        <tr>
          <td>18.5～24.9</td>
          <td>標準体重</td>
        </tr>
        <tr>
          <td>25～29.9</td>
          <td>過体重(肥満度Ⅰ)</td>
        </tr>
        <tr>
          <td>30～34.9</td>
          <td>肥満Ⅰ(Ⅱ)</td>
        </tr>
        <tr>
        <td>35～39.9</td>
          <td>肥満Ⅱ(Ⅲ)</td>
        </tr>
        <tr>
          <td>40～</td>
          <td>肥満Ⅲ(Ⅳ 極度の肥満)</td>
        </tr>
      </tbody>
    </table>

	</p>
    <p>
	    BMI
	= （除脂肪量 + 脂肪量）÷ 身長の2乗
	= （除脂肪量 ÷ 身長の2乗） + （脂肪量 ÷ 身長の2乗）
	=　FFMI　＋　FMI
	</p>
    
    
    <p>
	  鶏肉、牛肉、豚肉、魚介（鮭、サバ、ほっけ、あじ、イワシ、ほたて、えび、まぐろ赤身）、卵、納豆、ブロッコリー、じゃがいも、さつまいも、オクラ、ほうれん草、プロテイン、EAAの栄養素的なもの
	</p>
  </form>
</body>

</html>
