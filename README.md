# main code
import dash
from dash import dcc, html
from dash.dependencies import Input, Output
import pandas as pd

# Cargar los datos desde los archivos CSV
costos_df = pd.read_csv('costs_2022.csv')
ingresos_df = pd.read_csv('revenue_2022.csv')

# Imprimir los nombres de las columnas para verificar
print("Columnas en costs_2022.csv:", costos_df.columns)
print("Columnas en revenue_2022.csv:", ingresos_df.columns)

# Crear la aplicación Dash
app = dash.Dash(__name__)

# Diseño del layout
app.layout = html.Div([
    html.H1("Evolución de Ingresos y Costos de Servicios"),

    # Filtro de selección múltiple
    dcc.Dropdown(
        id='lineas-negocio-dropdown',
        options=[
            {'label': mes, 'value': mes} for mes in ingresos_df.columns[1:]
        ],
        multi=True,
        value=ingresos_df.columns[1:]
    ),

    # Gráfico
    dcc.Graph(id='evolucion-grafico')
])

# Callback para actualizar el gráfico según la selección del filtro
@app.callback(
    Output('evolucion-grafico', 'figure'),
    [Input('lineas-negocio-dropdown', 'value')]
)
def update_graph(selected_meses):
    # Crear el gráfico
    fig = {
        'data': [
            {'x': ingresos_df.columns[1:], 'y': ingresos_df.iloc[0, 1:], 'type': 'line', 'name': 'Ingresos'},
            {'x': costos_df.columns[1:], 'y': costos_df.iloc[0, 1:], 'type': 'line', 'name': 'Costos'}
        ],
        'layout': {
            'title': 'Evolución de Ingresos y Costos de Servicios',
            'xaxis': {'title': 'Mes'},
            'yaxis': {'title': 'Monto'}
        }
    }

    return fig

if __name__ == '__main__':
    app.run_server(debug=True)
