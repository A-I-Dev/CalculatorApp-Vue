<template>
    <div id="calc-outer-wrap">
        <div id="calc-inner-wrap">
            <div id="result-wrap">
                <p id="result">{{ result }}</p>
            </div>
            <div id="keys-wrap">
                <ul id="keys-list">
                    <li
                        v-for="key in keys"
                        :key="key.id"
                        :id="key.id"
                        :class="key.class"
                        :name="key.name"
                        @click="keyClick"
                    >
                        <span>{{ key.value }}</span>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "Calculator",
    data: function () {
        return {
            keys: [
                {
                    name: "digit-keys",
                    id: "zero-key",
                    class: "calc-key zero-key",
                    value: 0,
                },
                {
                    name: "digit-keys",
                    id: "one-key",
                    class: "calc-key",
                    value: 1,
                },
                {
                    name: "digit-keys",
                    id: "two-key",
                    class: "calc-key",
                    value: 2,
                },
                {
                    name: "digit-keys",
                    id: "three-key",
                    class: "calc-key",
                    value: 3,
                },
                {
                    name: "digit-keys",
                    id: "four-key",
                    class: "calc-key",
                    value: 4,
                },
                {
                    name: "digit-keys",
                    id: "five-key",
                    class: "calc-key",
                    value: 5,
                },
                {
                    name: "digit-keys",
                    id: "six-key",
                    class: "calc-key",
                    value: 6,
                },
                {
                    name: "digit-keys",
                    id: "seven-key",
                    class: "calc-key",
                    value: 7,
                },
                {
                    name: "digit-keys",
                    id: "eight-key",
                    class: "calc-key",
                    value: 8,
                },
                {
                    name: "digit-keys",
                    id: "nine-key",
                    class: "calc-key",
                    value: 9,
                },
                {
                    name: "special-keys",
                    id: "clear-key",
                    class: "calc-key",
                    value: "C",
                },
                {
                    name: "special-keys",
                    id: "clear-one-key",
                    class: "calc-key",
                    value: "CE",
                },
                {
                    name: "operator-keys",
                    id: "addition-key",
                    class: "calc-key",
                    value: "+",
                },
                {
                    name: "operator-keys",
                    id: "subtraction-key",
                    class: "calc-key",
                    value: "-",
                },
                {
                    name: "operator-keys",
                    id: "divide-key",
                    class: "calc-key",
                    value: "/",
                },
                {
                    name: "operator-keys",
                    id: "multiply-key",
                    class: "calc-key",
                    value: "*",
                },
                {
                    name: "special-keys",
                    id: "equal-key",
                    class: "calc-key equal-key",
                    value: "=",
                },
                {
                    name: "special-keys",
                    id: "decimal-point-key",
                    class: "calc-key",
                    value: ".",
                },
            ],
            result: "",
            allOperatorsRegEx: /[/*+-]/,
            allOperatorsArray: ["/", "*", "+", "-"],
            allDigitsRegEx: /[0-9]/,
            aloneNumberRegEx: /^[-]?[0-9.?]+$/,
            twoNumbersRegEx: /^[-]?[0-9.?]+[/*+-][0-9.?]+$/,
        };
    },
    methods: {
        keyClick: function (key) {
            switch (key.path[1].getAttribute("name")) {
                case "digit-keys":
                    this.result = this.digitKeysClick(key.path[1]);
                    break;
                case "operator-keys":
                    this.result = this.operatorKeysClick(key.path[1]);
                    break;
                case "decimal-point-key":
                    this.result = this.decimalPointKeyClick(key.path[1]);
                    break;
                case "special-keys":
                    switch (key.path[1].id) {
                        case "clear-key":
                            this.result = this.clearKeysClick(key.path[1]);
                            break;
                        case "clear-one-key":
                            this.result = this.clearKeysClick(key.path[1]);
                            break;
                        case "equal-key":
                            this.result = this.generateResult();
                            break;
                    }
                    break;
            }
            return this.result;
        },
        digitKeysClick: function (keyElement) {
            let resultFieldText = this.result,
                lastCharacter = resultFieldText.charAt(
                    resultFieldText.length - 1
                ),
                characterBeforeLast = resultFieldText.charAt(
                    resultFieldText.length - 2
                ),
                thisKeyElementText = keyElement.firstElementChild.textContent;
            if (
                resultFieldText.toString() !== "NaN" &&
                resultFieldText.toString() !== "Infinity" &&
                resultFieldText.toString() !== "-Infinity"
            ) {
                if (keyElement.id === "zero-key") {
                    if (
                        resultFieldText === "0" ||
                        (lastCharacter === "0" &&
                            this.allOperatorsRegEx.test(characterBeforeLast))
                    ) {
                        return resultFieldText;
                    } else {
                        resultFieldText += thisKeyElementText;
                    }
                } else {
                    if (resultFieldText === "0") {
                        resultFieldText = thisKeyElementText;
                    } else if (
                        lastCharacter === "0" &&
                        this.allOperatorsRegEx.test(characterBeforeLast)
                    ) {
                        resultFieldText =
                            resultFieldText.slice(
                                0,
                                resultFieldText.length - 1
                            ) + thisKeyElementText;
                    } else {
                        resultFieldText += thisKeyElementText;
                    }
                }
            }

            return resultFieldText;
        },
        operatorKeysClick: function (keyElement) {
            let resultFieldText = this.result,
                lastCharacter = resultFieldText.charAt(
                    resultFieldText.length - 1
                ),
                characterBeforeLast = resultFieldText.charAt(
                    resultFieldText.length - 2
                ),
                thisKeyElementText = keyElement.firstElementChild.textContent,
                firstCharacterIsMinus = resultFieldText.charAt(0) === "-",
                thereIsAnOperator = false;

            for (let i = 0; i < resultFieldText.length; i++) {
                if (firstCharacterIsMinus) {
                    firstCharacterIsMinus = false;
                    continue;
                } else {
                    if (this.allOperatorsRegEx.test(resultFieldText[i])) {
                        thereIsAnOperator = true;
                        break;
                    }
                }
            }

            if (
                resultFieldText.toString() !== "NaN" &&
                resultFieldText.toString() !== "Infinity" &&
                resultFieldText.toString() !== "-Infinity"
            ) {
                if (!thereIsAnOperator) {
                    if (
                        (lastCharacter === "." &&
                            !this.allOperatorsRegEx.test(
                                characterBeforeLast
                            )) ||
                        this.allOperatorsRegEx.test(lastCharacter)
                    ) {
                        resultFieldText =
                            resultFieldText.slice(
                                0,
                                resultFieldText.length - 1
                            ) + thisKeyElementText;
                    } else {
                        resultFieldText += thisKeyElementText;
                    }
                } else {
                    resultFieldText = this.generateResult();
                    if (
                        !this.allOperatorsRegEx.test(lastCharacter) &&
                        resultFieldText.toString() !== "NaN" &&
                        resultFieldText.toString() !== "Infinity" &&
                        resultFieldText.toString() !== "-Infinity"
                    ) {
                        resultFieldText += thisKeyElementText;
                    }
                }
            }

            return resultFieldText;
        },
        decimalPointKeyClick: function (keyElement) {
            let resultFieldText = this.result,
                thisKeyElementText = keyElement.firstElementChild.textContent;
            if (
                resultFieldText.toString() !== "NaN" &&
                resultFieldText.toString() !== "Infinity" &&
                resultFieldText.toString() !== "-Infinity"
            ) {
                if (!this.lastNumberHasDecimalPoint()) {
                    resultFieldText += thisKeyElementText;
                }
            }

            return resultFieldText;
        },
        lastNumberHasDecimalPoint: function () {
            let indexOfDot, indexOfMathChar;

            for (let i = 0; i < this.result.length; i++) {
                if (this.allOperatorsRegEx.test(this.result[i])) {
                    indexOfMathChar = i;
                }
                if (this.result[i] === ".") {
                    indexOfDot = i;
                }
            }

            if (indexOfDot !== undefined && indexOfMathChar === undefined) {
                return true;
            } else if (indexOfDot > indexOfMathChar) {
                return true;
            } else {
                return false;
            }
        },
        clearKeysClick: function (keyElement) {
            let resultFieldText = this.result,
                lastCharacter = resultFieldText.charAt(
                    resultFieldText.length - 1
                );
            if (
                resultFieldText === "NaN" ||
                resultFieldText === "Infinity" ||
                resultFieldText === "-Infinity"
            ) {
                resultFieldText = 0;
            } else {
                if (keyElement.id === "clear-key") {
                    resultFieldText = 0;
                } else {
                    if (!this.allOperatorsRegEx.test(lastCharacter)) {
                        if (this.aloneNumberRegEx.test(resultFieldText)) {
                            resultFieldText = 0;
                        } else {
                            let instanceOfResultFieldText,
                                additionalPosition,
                                positionOneIsMinus =
                                    resultFieldText.charAt(0) === "-";
                            if (positionOneIsMinus) {
                                instanceOfResultFieldText = resultFieldText.slice(
                                    1,
                                    resultFieldText.length
                                );
                                additionalPosition = 2;
                            } else {
                                instanceOfResultFieldText = resultFieldText;
                                additionalPosition = 1;
                            }

                            resultFieldText = resultFieldText.slice(
                                0,
                                (() => {
                                    let positionOfOperator;
                                    for (let i in this.allOperatorsArray) {
                                        if (
                                            instanceOfResultFieldText.indexOf(
                                                this.allOperatorsArray[i]
                                            ) > 0
                                        ) {
                                            positionOfOperator =
                                                instanceOfResultFieldText.indexOf(
                                                    this.allOperatorsArray[i]
                                                ) + additionalPosition;
                                        }
                                    }
                                    return positionOfOperator;
                                })()
                            );
                        }
                    } else {
                        resultFieldText = resultFieldText.slice(
                            0,
                            resultFieldText.length - 1
                        );
                    }
                }
            }

            return resultFieldText.toString();
        },
        generateResult: function () {
            let resultFieldText = this.result,
                lastCharacter = resultFieldText.charAt(
                    resultFieldText.length - 1
                ),
                indexOfLastOperator = 0,
                indexOfLastNumber = 0;
            for (let i = 0; i < resultFieldText.length; i++) {
                if (this.allOperatorsRegEx.test(resultFieldText[i])) {
                    indexOfLastOperator = i;
                }
                if (this.allDigitsRegEx.test(resultFieldText[i])) {
                    indexOfLastNumber = i;
                }
            }

            if (
                indexOfLastNumber > indexOfLastOperator &&
                indexOfLastOperator !== 0
            ) {
                if (
                    lastCharacter !== "." ||
                    !this.allOperatorsRegEx.test(lastCharacter)
                ) {
                    resultFieldText = this.calculate(resultFieldText);
                }
            }

            return resultFieldText.toString();
        },
        calculate: function (resultFieldText) {
            let nums = this.numbersCollector(resultFieldText),
                inputOperators = this.operatorCollector(resultFieldText),
                result = resultFieldText,
                power = 0;
            for (let num of nums) {
                if (num.indexOf(".") !== -1) {
                    if (power < num.length - 1 - num.indexOf(".")) {
                        power = num.length - 1 - num.indexOf(".");
                    }
                }
            }

            nums[0] = Math.floor(parseFloat(nums[0]) * Math.pow(10, power));
            nums[1] = Math.floor(parseFloat(nums[1]) * Math.pow(10, power));

            switch (inputOperators[0]) {
                case "/":
                    result = nums[0] / nums[1];
                    break;
                case "*":
                    result = (nums[0] * nums[1]) / Math.pow(10, power * 2);
                    break;
                case "+":
                    result = (nums[0] + nums[1]) / Math.pow(10, power);
                    break;
                case "-":
                    result = (nums[0] - nums[1]) / Math.pow(10, power);
                    break;
            }

            return result;
        },
        numbersCollector: function (resultFieldText) {
            let collectorsCounter = 0;
            let nums = ["", ""];

            for (let i = 0; i < resultFieldText.length; i++) {
                if (resultFieldText[i] === "-" && i === 0) {
                    nums[collectorsCounter] += resultFieldText[i];
                } else {
                    if (this.allOperatorsRegEx.test(resultFieldText[i])) {
                        collectorsCounter++;
                    } else {
                        nums[collectorsCounter] += resultFieldText[i];
                    }
                }
            }

            return nums;
        },
        operatorCollector: function (resultFieldText) {
            let collectorsCounter = 0;
            let inputOperators = [];

            for (let i = 0; i < resultFieldText.length; i++) {
                if (resultFieldText[i] === "-" && i === 0) {
                    continue;
                } else {
                    if (this.allOperatorsRegEx.test(resultFieldText[i])) {
                        inputOperators[collectorsCounter] = resultFieldText[i];
                        collectorsCounter++;
                    }
                }
            }

            return inputOperators;
        },
    },
};
</script>
