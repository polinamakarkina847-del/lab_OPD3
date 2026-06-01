from flask import Flask, request, render_template
import math

app = Flask(__name__)

def convert_to_radians(value, unit):

    if unit == 'degrees':
        return math.radians(value)
    else:
        return value

def calculate(function, value, precision):

    if function in ['asin', 'acos'] and (value < -1 or value > 1):
        return None, "Ошибка! arcsin и arccos работают только с числами от -1 до 1"
    
    try:
        if function == 'sin':
            result = math.sin(value)
        elif function == 'cos':
            result = math.cos(value)
        elif function == 'tan':
            result = math.tan(value)
        elif function == 'asin':
            result = math.asin(value)
        elif function == 'acos':
            result = math.acos(value)
        elif function == 'atan':
            result = math.atan(value)
        else:
            return None, "Неизвестная функция"

        rounded_result = round(result, precision)

        if abs(rounded_result) < 1e-12:
            rounded_result = 0.0

        return rounded_result, None
        
    except:
        return None, "Ошибка при вычислении"


@app.route('/', methods=['GET', 'POST'])
def index():

    result = None
    error = None

    if request.method == 'POST':
        try:
            function = request.form.get('function')
            unit = request.form.get('unit')
            value = float(request.form.get('value'))
            precision = int(request.form.get('precision'))

            if precision > 10:
                precision = 10
            if precision < 0:
                precision = 0

            if function in ['sin', 'cos', 'tan']:
                rad_value = convert_to_radians(value, unit)
            else:
                rad_value = value

            result, error = calculate(function, rad_value, precision)
            
        except ValueError:
            error = "Ошибка! Введите число (например: 30, 0.5, -45)"
        except Exception as e:
            error = f"Произошла ошибка: {str(e)}"

    return render_template('index.html', result=result, error=error)

if __name__ == '__main__':
    print("Сервер запущен!")
    print("Открой в браузере: http://127.0.0.1:5000")
    app.run(debug=True)
