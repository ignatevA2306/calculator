# calculator

<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="05-306" Height="550" Width="350">
    <Grid  Margin="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="70"/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>

        <TextBox x:Name="Screen" Grid.ColumnSpan="4" FontSize="36" TextAlignment="Right" VerticalContentAlignment="Center" Margin="5" IsReadOnly="True" Text="0"/>

        <!-- Первый ряд -->
        <Button Content="C" Grid.Column="0" Grid.Row="1" FontSize="30" Click="ClicSign" Margin="5" Background="#FFDDDDDD"/>
        <Button Content="←" Grid.Column="1" Grid.Row="1" FontSize="30" Click="ClicSign" Margin="5" Background="#FFDDDDDD"/>
        <Button Content="+/-" Grid.Column="2" Grid.Row="1" FontSize="30" Click="ChangeSign" Margin="5" Background="#FFDDDDDD"/>
        <Button Content="/" Grid.Column="3" Grid.Row="1" FontSize="30" Click="ClicSign" Margin="5" Background="#FFDDDDDD"/>

        <!-- Второй ряд -->
        <Button Content="7" Grid.Column="0" Grid.Row="2" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="8" Grid.Column="1" Grid.Row="2" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="9" Grid.Column="2" Grid.Row="2" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="*" Grid.Column="3" Grid.Row="2" FontSize="30" Click="ClicSign" Margin="5" Background="#FFDDDDDD"/>

        <!-- Третий ряд -->
        <Button Content="4" Grid.Column="0" Grid.Row="3" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="5" Grid.Column="1" Grid.Row="3" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="6" Grid.Column="2" Grid.Row="3" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="-" Grid.Column="3" Grid.Row="3" FontSize="30" Click="ClicSign" Margin="5" Background="#FFDDDDDD"/>

        <!-- Четвертый ряд -->
        <Button Content="1" Grid.Column="0" Grid.Row="4" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="2" Grid.Column="1" Grid.Row="4" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="3" Grid.Column="2" Grid.Row="4" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="+" Grid.Column="3" Grid.Row="4" FontSize="30" Click="ClicSign" Margin="5" Background="#FFDDDDDD"/>

        <!-- Пятый ряд -->
        <Button Content="0" Grid.Column="0" Grid.Row="5" FontSize="30" Click="OnClick" Margin="5" Grid.ColumnSpan="2"/>
        <Button Content="." Grid.Column="2" Grid.Row="5" FontSize="30" Click="OnClick" Margin="5"/>
        <Button Content="=" Grid.Column="3" Grid.Row="5" FontSize="30" Click="ClicSign" Margin="5" Background="#FFDDDDDD"/>
    </Grid>
</Window>

using System;
using System.Collections.Generic;
using System.Globalization;
using System.Windows;
using System.Windows.Controls;

namespace WpfApp1
{
    public partial class MainWindow : Window
    {
        private List<float> numbers = new List<float>();
        private string currentOperation = ""; 
        private bool operationClicked = false;

        public MainWindow()
        {
            InitializeComponent();
        }

        private void OnClick(object sender, RoutedEventArgs e)
        {
            Button button = (Button)sender;
            string content = button.Content.ToString();

            if (operationClicked)
            {
                Screen.Text = content;
                operationClicked = false;
            }
            else
            {
                if (content == ".")
                {
                    if (!Screen.Text.Contains("."))
                    {
                        Screen.Text = Screen.Text == "0" ? "0." : Screen.Text + ".";
                    }
                }
                else
                {
                    Screen.Text = Screen.Text == "0" ? content : Screen.Text + content;
                }
            }
        }

        // Этот метод обрабатывает нажатия на кнопки операций (C, ←, +, -, *, /, =)
        private void ClicSign(object sender, RoutedEventArgs e)
        {
            Button button = (Button)sender;
            string sign = button.Content.ToString();

            switch (sign)
            {
                case "C":
                    ClearCalculator();
                    break;
                case "←":
                    Backspace();
                    break;
                case "+":
                case "-":
                case "*":
                case "/":
                    PerformOperation(sign);
                    break;
                case "=":
                    CalculateResult();
                    break;
            }
        }

        private void ChangeSign(object sender, RoutedEventArgs e)
        {
            if (Screen.Text != "0") 
            {
                if (Screen.Text.StartsWith("-"))
                {
                    Screen.Text = Screen.Text.Substring(1); 
                }
                else
                {
                    Screen.Text = "-" + Screen.Text; 
                }
            }
        }


        // Этот метод вызывается, когда пользователь нажимает кнопку арифметической операции (+, -, *, /).
        private void PerformOperation(string operation)
        {
            if (float.TryParse(Screen.Text, NumberStyles.Float, CultureInfo.InvariantCulture, out float number))
            {
                numbers.Add(number);

                if (numbers.Count > 1 && !string.IsNullOrEmpty(currentOperation))
                {
                    CalculateIntermediateResult(); // Вычисляем промежуточный результат, если есть предыдущие числа и операция
                }

                currentOperation = operation;
                operationClicked = true; // Готовимся к вводу следующего числа
            }
            else
            {
                Screen.Text = "Ошибка ввода";
            }
        }

        private void CalculateResult()
        {
            if (float.TryParse(Screen.Text, NumberStyles.Float, CultureInfo.InvariantCulture, out float number))
            {
                numbers.Add(number);
            }
            else
            {
                Screen.Text = "Ошибка ввода";
                return;
            }

            if (numbers.Count > 0 && !string.IsNullOrEmpty(currentOperation))
            {
                CalculateIntermediateResult();
            }
            else if (numbers.Count == 1)
            {
                Screen.Text = numbers[0].ToString(CultureInfo.InvariantCulture);
            }
            ClearIntermediateData();
        }

        //Функция, которая выполняет арифметические операции над числами
        private void CalculateIntermediateResult()
        {
            if (numbers.Count < 2) return;

            float result = numbers[0];  
            numbers.RemoveAt(0);     

            foreach (float number in numbers)
            {
                switch (currentOperation)
                {
                    case "+":
                        result += number;
                        break;
                    case "-":
                        result -= number;
                        break;
                    case "*":
                        result *= number;
                        break;
                    case "/":
                        if (number != 0)
                        {
                            result /= number;
                        }
                        else
                        {
                            Screen.Text = "Деление на ноль!";
                            return;
                        }
                        break;
                }
            }
            numbers.Clear();           //Очищаем список чисел, чтобы сохранить только результат
            numbers.Add(result);       //Добавляем результат в список
            Screen.Text = result.ToString(CultureInfo.InvariantCulture); // Отображаем результат
        }

        private void ClearCalculator()
        {
            Screen.Text = "0";
            numbers.Clear();
            currentOperation = "";
            operationClicked = false;
        }

        private void ClearIntermediateData()
        {
            numbers.Clear();
            currentOperation = "";
            operationClicked = false;
        }

        private void Backspace()
        {
            if (Screen.Text.Length > 0)
            {
                Screen.Text = Screen.Text.Substring(0, Screen.Text.Length - 1);
                if (Screen.Text.Length == 0)
                {
                    Screen.Text = "0";
                }
            }
        }
    }
}
