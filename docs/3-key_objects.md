# Key Objects

[_Documentation generated by Documatic_](https://www.documatic.com)

<!---Documatic-section-parse_files.read_schema-start--->
## parse_files.read_schema

<!---Documatic-section-read_schema-start--->
<!---Documatic-block-parse_files.read_schema-start--->
<details>
	<summary><code>parse_files.read_schema</code> code snippet</summary>

```python
def read_schema(filename):
    fields = load_schema(str(filename), context={'cpf': CPFField, 'text': rows.fields.TextField, 'decimal': CustomDecimalField, 'date': rows.fields.DateField, 'integer': rows.fields.IntegerField})
    for row in rows.import_from_csv(filename):
        fields[row.field_name] = fields[row.field_name]()
        fields[row.field_name].optional = 'optional' in (row.options or '')
    return fields
```
</details>
<!---Documatic-block-parse_files.read_schema-end--->
<!---Documatic-section-read_schema-end--->

# #
<!---Documatic-section-parse_files.read_schema-end--->

<!---Documatic-section-utils.is_court_name_equivalent-start--->
## utils.is_court_name_equivalent

<!---Documatic-section-is_court_name_equivalent-start--->
```mermaid
flowchart LR
utils.is_court_name_equivalent-->utils.fix_tribunal
```

### Object Calls

* utils.fix_tribunal

<!---Documatic-block-utils.is_court_name_equivalent-start--->
<details>
	<summary><code>utils.is_court_name_equivalent</code> code snippet</summary>

```python
def is_court_name_equivalent(a, b):

    def replace_names(a):
        return slug(fix_tribunal(a)).replace('vantagens_', 'direitos_').replace('trt_', 'tribunal_regional_do_trabalho_').replace('trf_', 'tribunal_regional_federal_').replace('justica_federal_', 'tribunal_regional_federal_')
    return Levenshtein.distance(replace_names(a), replace_names(b)) <= 3
```
</details>
<!---Documatic-block-utils.is_court_name_equivalent-end--->
<!---Documatic-section-is_court_name_equivalent-end--->

# #
<!---Documatic-section-utils.is_court_name_equivalent-end--->

<!---Documatic-section-parse_files.merge_header_lines-start--->
## parse_files.merge_header_lines

<!---Documatic-section-merge_header_lines-start--->
<!---Documatic-block-parse_files.merge_header_lines-start--->
<details>
	<summary><code>parse_files.merge_header_lines</code> code snippet</summary>

```python
def merge_header_lines(first, second):
    result = []
    for (value1, value2) in zip(first, second):
        if value1 is None and value2 is None:
            result.append(None)
        elif value1 is None:
            result.append(value2)
        elif value2 is None:
            result.append(value1)
        else:
            result.append(f'{value1} {value2}')
    return result
```
</details>
<!---Documatic-block-parse_files.merge_header_lines-end--->
<!---Documatic-section-merge_header_lines-end--->

# #
<!---Documatic-section-parse_files.merge_header_lines-end--->

<!---Documatic-section-parse_files.is_filled-start--->
## parse_files.is_filled

<!---Documatic-section-is_filled-start--->
<!---Documatic-block-parse_files.is_filled-start--->
<details>
	<summary><code>parse_files.is_filled</code> code snippet</summary>

```python
def is_filled(row):
    null_set = {'', Decimal('0'), None, '0', '***.***.***-**'}
    values = set([str(value or '').strip() for value in row.values()])
    values_are_filled = not values.issubset(null_set)
    has_name = (row['nome'] or '').strip() not in ('', '0')
    return values_are_filled and has_name
```
</details>
<!---Documatic-block-parse_files.is_filled-end--->
<!---Documatic-section-is_filled-end--->

# #
<!---Documatic-section-parse_files.is_filled-end--->

<!---Documatic-section-parse_files.fix_header-start--->
## parse_files.fix_header

<!---Documatic-section-fix_header-start--->
<!---Documatic-block-parse_files.fix_header-start--->
<details>
	<summary><code>parse_files.fix_header</code> code snippet</summary>

```python
def fix_header(sheet_name, header):
    name = sheet_name
    if '-' in name:
        name = name.split(' - ')[1]
    sheet_slug = slug(name)
    new_header = []
    for value in header:
        field_name = slug(regexp_parenthesis.sub('', value or '').strip())
        if value is None or 'deverao_ser_preenchidos' in field_name or 'observacao' in field_name or (field_name in ('sjsp', 'trf', 'sjms')):
            break
        field_name = field_name.replace('vant_art_', 'vantagens_artigo_').replace('vantagens_art_', 'vantagens_artigo_').replace('outra_1_dirpes', 'outra').replace('outra_2_dirpes', 'outra_2').replace('outra_1_direvent', 'outra').replace('outra_2_direvent', 'outra_2').replace('outra_1', 'outra').replace('gratificacao_presidencia', 'gratificacao_de_presidencia').replace('vantagens_eventuavs_', 'vantagens_eventuais_').replace('auxilioalimentacao', 'auxilio_alimentacao').replace('auxilio_preescolar', 'auxilio_pre_escolar').replace('correcao_monetariajuros', 'correcao_monetaria_juros').replace('vantagens_eventuais', 'direitos_eventuais').replace('vantagens_pessoais', 'direitos_pessoais')
        if field_name.startswith(sheet_slug):
            field_name = field_name[len(sheet_slug):]
        elif field_name.startswith('vantagens_eventuais_'):
            field_name = field_name[len('vantagens_eventuais_'):]
        elif field_name in ('subsidio_total_de', 'subsidio_outra', 'subsidio_outra_detalhe'):
            field_name = field_name.replace('subsidio_', '')
        field_name = slug(field_name)
        if field_name.endswith(sheet_slug):
            field_name = field_name[:-len(sheet_slug)]
        elif field_name.endswith('_vantagens_pessoais'):
            field_name = field_name[:-len('_vantagens_pessoais')]
        field_name = slug(field_name)
        if field_name in ('total_de', 'total_de_'):
            field_name = 'total'
        elif field_name == 'cargo_origem':
            field_name = 'cargo_de_origem'
        elif field_name == 'outra_detalhe':
            field_name = 'detalhe'
        elif field_name == 'outra_pae':
            field_name = 'parcela_autonoma_de_equivalencia'
        elif field_name == 'previdencia_publica':
            field_name = 'descontos_previdencia_publica'
        elif field_name == 'vantagens_artigo_184_e_192_lei_171152':
            field_name = 'vantagens_artigo_184_i_e_192_i_lei_171152'
        elif field_name == 'abono_constitucional_de_1_3_de_ferias':
            field_name = 'abono_constitucional_de_13_de_ferias'
        elif field_name == 'gratificacao_por_encargo_cursoconcurso':
            field_name = 'gratificacao_por_encargo_curso_concurso'
        new_header.append(field_name)
    header = make_header(new_header)
    schema = SHEET_INFO[sheet_name]['schema']
    reference_header = list(schema.keys())
    diff1 = set(reference_header) - set(header)
    diff2 = set(header) - set(reference_header)
    for (field_name, field_type) in schema.items():
        if field_name in diff1 and field_type.optional:
            diff1.remove(field_name)
    if diff1 or diff2:
        if len(diff1) > 1 or len(diff2) > 1 or len(diff1) != len(diff2):
            raise ValueError(f'Invalid header: {header} (expected: {reference_header}). A - B: {diff2}, B - A: {diff1}')
        header[header.index(diff2.pop())] = diff1.pop()
    return header
```
</details>
<!---Documatic-block-parse_files.fix_header-end--->
<!---Documatic-section-fix_header-end--->

# #
<!---Documatic-section-parse_files.fix_header-end--->

<!---Documatic-section-parse_files.make_fields-start--->
## parse_files.make_fields

<!---Documatic-section-make_fields-start--->
<!---Documatic-block-parse_files.make_fields-start--->
<details>
	<summary><code>parse_files.make_fields</code> code snippet</summary>

```python
def make_fields(sheet_name, header):
    reference_fields = SHEET_INFO[sheet_name]['schema']
    fields = OrderedDict()
    for key in header:
        fields[key] = reference_fields[key]
    return fields
```
</details>
<!---Documatic-block-parse_files.make_fields-end--->
<!---Documatic-section-make_fields-end--->

# #
<!---Documatic-section-parse_files.make_fields-end--->

<!---Documatic-section-parse_files.make_row-start--->
## parse_files.make_row

<!---Documatic-section-make_row-start--->
<!---Documatic-block-parse_files.make_row-start--->
<details>
	<summary><code>parse_files.make_row</code> code snippet</summary>

```python
def make_row(fields, row_values):
    row = {field_name: field_type.deserialize(row_values[index]) for (index, (field_name, field_type)) in enumerate(fields.items())}
    return row
```
</details>
<!---Documatic-block-parse_files.make_row-end--->
<!---Documatic-section-make_row-end--->

# #
<!---Documatic-section-parse_files.make_row-end--->

<!---Documatic-section-utils.fix_tribunal-start--->
## utils.fix_tribunal

<!---Documatic-section-fix_tribunal-start--->
<!---Documatic-block-utils.fix_tribunal-start--->
<details>
	<summary><code>utils.fix_tribunal</code> code snippet</summary>

```python
def fix_tribunal(tribunal):
    if tribunal is None:
        return None
    tribunal = regexp_tribunal_ordinal.sub('\\1ª ', regexp_tribunal_fim.sub('', tribunal).replace('/ ', '/').replace(' /', '/'))
    result = []
    for word in tribunal.title().split():
        if word in ('Da', 'Das', 'De', 'Do', 'Dos', 'E'):
            word = word.lower()
        elif word == 'Trf':
            word = 'Tribunal Regional Federal da'
        elif word == 'Trt':
            word = 'Tribunal Regional do Trabalho da'
        elif word == 'Tre':
            word = 'Tribunal Regional Eleitoral do'
        elif word == 'Tre-Am':
            word = 'Tribunal Regional Eleitoral do Amazonas'
        elif word == 'Regiao':
            word = 'Região'
        elif word == 'Justica':
            word = 'Justiça'
        elif word == '-':
            word = 'da'
        elif word == 'Df':
            word = 'Distrito Federal e Territórios'
        elif word == 'Terceira':
            word = '3ª'
        elif word.endswith('A.'):
            word = regexp_ordinal.sub('da \\1ª', word)
        elif word.endswith('ª'):
            word = 'da ' + word
        elif word == 'Tjpa':
            word = 'do Pará'
        elif word == 'Ms':
            word = 'Mato Grosso do Sul'
        elif word == 'Rn':
            word = 'Rio Grande do Norte'
        if 'º' in word:
            word = word.replace('º', 'ª')
        result.append(word)
    result = ' '.join(result).replace('Mili Tar', 'Militar').replace('Regional Federal', 'Regional Federal da').replace(' e dos Territórios', ' e Territórios').replace(' e Seccionais', '').replace(' da da ', ' da ').replace(' da da ', ' da ').replace(' da da ', ' da ').replace(' da do ', ' do ').replace(' do Estado de ', ' do ').replace(' do Estado do ', ' do ').replace(' Estado do ', ' do ').replace('Tribunal Justiça ', 'Tribunal de Justiça ').replace('do Pará do Pará', 'do Pará').replace('do Rondônia', 'de Rondônia').replace('do São Paulo', 'de São Paulo').replace('de Bahia', 'da Bahia').replace('de Goiás da Go', 'do Goiás').replace('de Mato Grosso', 'do Mato Grosso').replace('do Minas Gerais', 'de Minas Gerais').replace('de Sergipe', 'do Sergipe')
    if 'Justiça Federal' in result and result != 'Conselho da Justiça Federal':
        result = result.replace('Justiça Federal', 'Tribunal Regional Federal')
    if result.endswith('ª'):
        result += ' Região'
    return result
```
</details>
<!---Documatic-block-utils.fix_tribunal-end--->
<!---Documatic-section-fix_tribunal-end--->

# #
<!---Documatic-section-utils.fix_tribunal-end--->

[_Documentation generated by Documatic_](https://www.documatic.com)