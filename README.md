# EMI-calculator
----
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Loan EMI Calculator - Sonu Taraji</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <style>
        *{
            box-sizing: border-box;
            margin:0;
            padding:0;
        }

        body{
            font-family: Arial, Helvetica, sans-serif;
            background:#f0f2f5;
            padding:20px;
        }

        .container{
            max-width: 700px;
            margin:0 auto;
            background:#fff;
            border:1px solid #ddd;
            border-radius:8px;
            padding:18px 18px 22px;
        }

        .header{
            text-align:center;
            margin-bottom:15px;
        }

        .header h1{
            font-size:22px;
        }

        .header p{
            font-size:12px;
            color:#666;
            margin-top:4px;
        }

        .row{
            display:flex;
            gap:14px;
            flex-wrap:wrap;
            margin-bottom:10px;
        }

        .field{
            flex:1 1 200px;
            display:flex;
            flex-direction:column;
            margin-bottom:5px;
        }

        label{
            font-size:13px;
            margin-bottom:4px;
        }

        input[type="number"]{
            padding:8px 9px;
            border-radius:5px;
            border:1px solid #ccc;
            font-size:13px;
        }

        input[type="number"]:focus{
            outline:none;
            border-color:#1d4ed8;
        }

        .hint{
            font-size:11px;
            color:#777;
            margin-top:2px;
        }

        .buttons{
            margin-top:10px;
            display:flex;
            gap:10px;
            flex-wrap:wrap;
        }

        button{
            padding:8px 14px;
            border-radius:5px;
            border:none;
            font-size:13px;
            cursor:pointer;
        }

        .btn-main{
            background:#1d4ed8;
            color:#fff;
        }

        .btn-reset{
            background:#f3f4f6;
            border:1px solid #ddd;
        }

        .error{
            font-size:11px;
            color:#b91c1c;
            margin-top:6px;
            display:none;
        }

        .result-box{
            margin-top:15px;
            padding:10px 12px;
            border-radius:6px;
            background:#f9fafb;
            border:1px solid #e5e7eb;
        }

        .result-box h3{
            font-size:15px;
            margin-bottom:6px;
        }

        .result-item{
            font-size:13px;
            margin-bottom:3px;
        }

        .note{
            margin-top:10px;
            font-size:11px;
            color:#555;
            line-height:1.4;
        }

        .formula{
            margin-top:8px;
            font-size:11px;
            background:#f3f4ff;
            border:1px dashed #cbd5f5;
            padding:6px 8px;
            border-radius:6px;
        }

        @media (max-width: 480px){
            .container{
                padding:14px;
            }
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        <h1>Loan EMI Calculator</h1>
        <p>Created by <b>Sonu Taraji</b> &nbsp;|&nbsp; Redington Project</p>
    </div>

    <!-- Input area -->
    <div class="row">
        <div class="field">
            <label for="amount">Loan Amount (₹)</label>
            <input type="number" id="amount" placeholder="Example: 250000" min="1">
        </div>

        <div class="field">
            <label for="rate">Annual Interest Rate (%)</label>
            <input type="number" id="rate" placeholder="Example: 10.5" step="0.01" min="0">
        </div>
    </div>

    <div class="row">
        <div class="field">
            <label for="months">Tenure in Months</label>
            <input type="number" id="months" placeholder="Example: 36" min="1">
            <span class="hint">You can also enter years instead of months below.</span>
        </div>

        <div class="field">
            <label for="years">Tenure in Years (optional)</label>
            <input type="number" id="years" placeholder="Example: 3" min="0">
            <span class="hint">If you fill years, it will be converted to months.</span>
        </div>
    </div>

    <p id="errorMsg" class="error">Please fill all fields with valid values.</p>

    <div class="buttons">
        <button class="btn-main" onclick="calcEmi()">Calculate EMI</button>
        <button class="btn-reset" onclick="clearAll()">Reset</button>
    </div>

    <!-- Result area -->
    <div class="result-box" id="resultBox">
        <h3>Result</h3>
        <p class="result-item">Monthly EMI: <b>₹ <span id="emiOut">0.00</span></b></p>
        <p class="result-item">Total Payment (Principal + Interest): <b>₹ <span id="totalOut">0.00</span></b></p>
        <p class="result-item">Total Interest Payable: <b>₹ <span id="interestOut">0.00</span></b></p>
        <p class="result-item" id="tenureText"></p>
    </div>

    <div class="note">
        This is a simple web-based EMI calculator created using HTML, CSS and JavaScript.
        It is useful for understanding how much EMI we have to pay for a given loan.
    </div>

    <div class="formula">
        <b>Formula used:</b><br>
        EMI = (P × r × (1 + r)<sup>n</sup>) / ((1 + r)<sup>n</sup> − 1)<br>
        P = Loan Amount, r = Monthly Interest Rate, n = Number of Months
    </div>
</div>

<script>
    // Main function to calculate EMI
    function calcEmi(){
        var amount = parseFloat(document.getElementById("amount").value);
        var rateYear = parseFloat(document.getElementById("rate").value);
        var months = parseInt(document.getElementById("months").value);
        var years = parseInt(document.getElementById("years").value);
        var error = document.getElementById("errorMsg");

        // convert years to months if user filled years
        if((isNaN(months) || months <= 0) && !isNaN(years) && years > 0){
            months = years * 12;
            document.getElementById("months").value = months;  // show converted months
        }

        if(isNaN(amount) || amount <= 0 || isNaN(rateYear) || rateYear < 0 || isNaN(months) || months <= 0){
            error.style.display = "block";
            showResult(0,0,0,0);
            return;
        } else {
            error.style.display = "none";
        }

        var monthlyRate = rateYear / 12 / 100;
        var emi;

        if(monthlyRate === 0){
            // no interest case
            emi = amount / months;
        } else {
            var x = Math.pow(1 + monthlyRate, months);
            emi = (amount * monthlyRate * x) / (x - 1);
        }

        var total = emi * months;
        var interest = total - amount;

        showResult(emi, total, interest, months);
    }

    // Update result section
    function showResult(emi, total, interest, months){
        document.getElementById("emiOut").textContent = emi.toFixed(2);
        document.getElementById("totalOut").textContent = total.toFixed(2);
        document.getElementById("interestOut").textContent = interest.toFixed(2);

        var tenureText = document.getElementById("tenureText");
        if(months > 0){
            tenureText.textContent = "Tenure considered: " + months + " month(s).";
        } else {
            tenureText.textContent = "";
        }
    }

    // Clear all fields and reset result
    function clearAll(){
        document.getElementById("amount").value = "";
        document.getElementById("rate").value = "";
        document.getElementById("months").value = "";
        document.getElementById("years").value = "";
        document.getElementById("errorMsg").style.display = "none";
        showResult(0,0,0,0);
    }
</script>

</body>
</html>
