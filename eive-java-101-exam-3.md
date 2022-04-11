1) No Maven o arquivo `pom.xml` é o principal arquivo de configuração de dependencias. Sabendo disso leia as afirmativas abaixo.

I - A dependências podem ser adicionadas diretamente na pasta lib do projeto e este após compilar continua reconhecendo. 
II - A dependências seguem um padrão de organização, sempre devem ser adicionadas na sessão `<dependencies>` do arquivo. 
III - Sempre que uma nova depenência é adicionada no projeto o `Mavem` identifica e baixa de forma altomatica sem que o desenvolvedor precise se preocupar.

Assinale a alternativa que corresponte apenas as informações verdadeiras.

- [ ] III, apenas
- [ ] I e II
- [ ] I e III 
- [ ] II e III
- [x] I, II e III


2) No `Maven` existem alguns comandos que são conhecidos como `goal`, com base no seu conhecimento, leia as afirmativas abaixo e assinale a alternativa correta

I - O comando `mvn test` tem como finalidade executar os testes do projeto
II - O comando `mvn deploy production` é usado quando precisa gerar o artefado para o ambiente de produção
III - `mvn install` é o goal que realiza a instalação do projeto no diretório local

- [ ] I, apenas
- [ ] II apenas
- [ ] I e II apenas
- [x] I e III apenas
- [ ] II e III, apenas


3) Supondo que existe um banco de dados onde neste banco de dados existe uma tabela com o nome `Produto` e nesta tabela temos dois registros: 

 ID = 1
 NOME = TELEVISÃO
 DESCRICAO = TV 52"

 ID = 2
 NOME = RÁRIO
 DESCRICAO = RÁDIO AM/FM

 Dado o seguinte código, qual o resultado é impresso no console?
```java
  /
  Statement stmt = connection.createStatement();
  stmt.execute("Select id, nome, descricao From Produto");
  ResultSet rst = stmt.getResultSet();

  while(rst.next()){
      System.out.print(rst.getInt("id"));
      System.out.print(rst.getString("nome"));
      System.out.print(rst.getString("descricao"));
      System.out.println("");
  }
  connection.close();
  
//Não vai compilar por conta de estar faltando criar a coneção com o banco atraves da Interface Connection. Se tivesse a conexão funcionaria corretamente, os get retornaria os dados que tem no banco(id, nome, descricao).
```


4) Veja o comando abaixo, como ele poderia ser adaptado para imprimir o código gerado na inclusão do registro?
```java
  Statement stmt = connection.createStatement();
  stmt.execute("INSERT INTO Produto VALUES ('Cadeira', 'Cadeira 4 pernas')");  

-------------------------------------------------------------------------------------
    //Dado que tenha a conexão com o banco
    
    Statement stmt = connection.createStatement();
        stmt.execute("INSERT INTO Produto (NOME, DESCRICAO) VALUES ('Cadeira', 'Cadeira 4 pernas')"
                    , Statement.RETURN_GENERATED_KEYS);
        ResultSet rst = stmt.getGeneratedKeys();
        while(rst.next()){
            Integer id = rst.getInt(1);
            System.out.println("ID" + id);
        }
```

5. Oque é um pool de conexão e qual sua vantagem? Explique.

   - É uma camada que fica entre o cliente de banco de dados. A ideia é que o cliente possa criar conexões com o banco usando um pool de conexão. Ele mantém certo número de conexões abertas com o banco de dados, quando é aberta uma conexão usando o pool, ao invés de abrir uma nova conexão com o banco, este simplesmente pega uma das conexões que ele já contém aberta com o banco e a marca como alocada para o cliente. Quando o cliente fecha a conexão usando o pool ele não fecha a conexão com o banco, ele mantém aberta, mais a maca como disponível.

     

6. De um exemplo de uso de pool de conexão usando a biblioteca c3p0

   - ```java
     //Para usar a lib c3p0, tem que adicionar a lib no projeto, ou adicionar nas depência no gerenciador de depência de preferência. 
     
     public class ConnectionFactory {
     
         public DataSource dataSource;
     
         public ConnectionFactory() {
             //Usando o ComboPooledDataSource para adquirir o DataSources, e definindo as propriedades de acesso.
              ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
             comboPooledDataSource.setJdbcUrl("URL");
             comboPooledDataSource.setUser("USER");
             comboPooledDataSource.setPassword("SENHA");
     
             this.dataSource = comboPooledDataSource;
             //Passando para o dataSource o as propriedades cedatadas no comboPooledDataSource.
         }
     
         public Connection recuperaConexao() throws SQLException {
             return this.dataSource.getConnection();
             //Solicitando para o dataSource uma conexão
         }
     }
     ```

     

7. Quais as desvantagens de usar o JDBC em uma aplicação?

   - Com JDBC, se escreve muito mais código, enquanto que em um framework ORM para gerenciar as transações e as conexões com o banco de dados é muito mais simples, e também com um framework ORM é possível fazer Querys com orientação a objetos, enquanto que com JDBC se trabalha com o modelo relacional.

     

8. Explique de forma sucinta como funciona o ciclo de vida de uma aplicação JPA

   - No JPA um objeto pode assumir quatro estado: TRANSIENT, MANAGED e DETACHED. E com decorrer das implementações feitas, esses estados podem mudar de acordo com oque for necessário.

     

9. Sobre JPA explique a diferença entre o uso da importação de `javax.persistence.Entity` e `org.hibernate.annotations.Entity`

   - javax.persistence.Entity -> É a anotação da especificação da JPA.
   - org.hibernate.annotations.Entity -> É a anotação da especificação do Hibernate.
     - Usando a importação do javax.persistence.Entity, não ficará preso ao Hibernate, sendo assim você poderia teoricamente executar seu código em outras implementações de JPA.

10) Dado a estrutura de uma tabela `Carros`, escreva como seria o mapeamento em JAVA usando o JPA 

```SQL
   Create Table Cores(
      ID_COR INT auto_increment,
      NOME VARCHAR(50),
      PRIMARY KEY(ID_COR)
   );

   Create Table Carros(
      ID_CARRO INT auto_increment,
      MARCA VARCHAR(50),      
      MODELO VARCHAR(50),
      PLACA VARCHAR(10),
      ID_COR INT,
      PRIMARY KEY(ID_CARRO)
   );

   ALTER TABLE Carros ADD FOREIGN KEY(ID_COR) REFERENCES Cores (ID_COR);
```

```java
//Dado que eu tenha o persistence.xml configurado..

import javax.persistence.*;

@Entity
public class Carros {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id_carro;
    private String marca;
    private String modelo;
    private String placa;
    private int id_cor;

    @ManyToOne
    private Cores cores;

    public Carros() {
    }

    public Carros(String marca, String modelo, String placa, int id_cor) {
        this.marca = marca;
        this.modelo = modelo;
        this.placa = placa;
        this.id_cor =  id_cor;
    }

    //Getter
}
```

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Cores {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id_cor;
    private String nome;

    public Cores() {
    }

    public Cores(String nome) {
        this.nome = nome;
    }

    //Getter
}

```



11. Sobre o ciclo de vida de uma Entidade, Explique cada um dos estados `TRANSIENT`, `MANAGED` e `DETACHED`.

    - TRANSIENT -> Para se entrar nesse estado tem que instanciar uma Entidade. O estado transien é de uma entidade que nunca foi persistida, não está gravada no banco de dados, não tem um *id* e não está sendo gerenciada pela JPA.

    - MANAGED -> Para se entrar nesse estado é só após fazer a chamada do método persist(). Managed é o principal estado que uma entidade pode estar, tudo que acontece com uma entidade nesse estado, a JPA observará e poderá sincronizar com o banco de dados.

    - DETACHED -> Só entrará nesse estado após dar um close() ou clear(). Se os dados estavam salvos, a Entidade entra no estado DETACHED. Esse estado em que a entidade não é mais transient, porque tem id, já foi salva no banco de dados, porém, não está mais sendo gerenciada

      

12. Qual a finalidade do método `merge()` no JPA. Escreva um exemplo simples para mostrar seu uso.

    - merge() -> Pega uma entidade que está no estado detached e retorná-la ao estado managed.

      ```java
        EntityManager entityManager = JPAUtil.getEntityManager();        
        entityManager.getTransaction().begin();
      
        entityManager.persist(carros);
        carros.setNome("Monza");    
      
        entityManager.flush();
        entityManager.clear();
      
        entityManager.merge(carros);
        carros.setNome("Fusca")
        entityManager.flush();
      ```

      

13) Usando a sintaxe JPQL como poderia ser adaptado o código abaixo para listar todos os dados da tabela `Carros`

```java

  public Lista<Carro> findAll(){
    return null;
  }

//Adaptação
public List<Carros> findAll(){
	return entityManager.createQuery*("SELECT c FROM Carros c", Carros.class).getResultList();
}
```

14) Usando a sintaxe JPQL avalie o código abaixo e sinalize quais pontos estão errados no código

```java
  public Lista<Pessoa> findById(Long id) {
      					
     //Faltou indicar no lugar do (*) o alias(P), dizendo para carregar o proprio objeto (P).
     //E para indicar um parâmetro se usa (:parâmetro) ou (?1).
      String consulta = "Select * from Pessoas P Where P.ID_PESSOA = ?";
      entityManager.createStatement(consulta);
      return entityManager.createQuery(consulta, Pessoa.class)
          //Quando for setar os parâmetro (nomeParametro, valorParaSubstituir). 
          //No se tivesse passado o parâmetro de forma correta, ficaria (1, id).
         .setParameter(1, 1)
         .getResultList(); 
  } 
```



15. Qual a função do `mappedBy` e onde ele deve ser usado?

    - Usando quando se tem um relacionamento bidirecional, é para indicar qual é o lado inverso ou não dominante da relação.

    - É mappedBy  é usando sempre na associação que não é o dono da relação. Ele é um atributo para ser utilizado nas annotations @OneToMany, @OneToOne, @ManyToMany. 

    - Ex: @ManyToMany(mappedBy = "atributo").

      

16. Escreva um exemplo de como fazer um `JOIN` no JPQL?

    ````java
    "SELECT carros.id_cor, cores.id_cor FROM Carros carros JOIN carros.cores cores"
    ````

17. Qual a função do `select new` no JPA?

    - Usando na cláusula SELECT para passar um construtor, para retornara uma ou mais instâncias.


18) Considerando que no seu projeto tem a seguinte estrutura de pacotes:

```java
  br.com.curso.modelo
         Carro.java
         Cores.java
  br.com.curso.dao
         CarroDao.java
         CoresDao.java
```

  Escreva um método que exemplifique uma consulta usando `select new` do JPA, descreva se precisa de novas classes e se for preciso onde cada uma delas deve ser organizada no projeto seguindo as boas praticas.

````java
package br.com.curso.vo;
    
public class CarroVo {
	private String marca;
    private String modelo;
    private String placa;
    private int id_cor;
    
    public CarroVo(String marca, String modelo, String placa, int id_cor) {
        this.marca = marca;
        this.modelo = modelo;
        this.placa = placa;
        this.id_cor = id_cor;
    }
    
    //Getter
}
````

````java
public List<CarroVo> nomeMetodo() {
    String consultaJpql = "SELECT new CarroVo(carros.marca, carros.modelo, carros.placa, cores.id_cor) FROM Carros carros JOIN carros.cores cores""
}
````




19) Sobre a configuração de conexão com banco de dados no Spring leia as afirmativas abaixo:

I. Para conexão com o banco de dados é necessário apenas adicionar as dependências no arquivo `pom.xml`
II. As configurações de acesso ao banco de dados devem ficar no arquivo `application.propriedades`
III. A definição em `spring.jpa.hibernate.ddl-auto=update` é usada para dar atualizar as informações do banco de dados

Assinale apenas as alternativas que correspondem a afirmações verdadeiras

- [ ] I, apenas
- [ ] I e II, apenas
- [x] II e III, apenas
- [ ] I, II e III
- [ ] nenhuma das afirmativas

20. Escreva um exemplo de implementação e uso de um `CrudRepository` para a classe da entidade `Cores.java`.

    ````java
    //package and import 
    
    @Service
    public class CrudCoresService {
    
        private Boolean system = true;
        private final CoresRepository coresRepository;
    
        public CrudCoresService(coresRepository coresRepository) {
            this.coresRepository = coresRepository;
        }
    
        public void inicial(Scanner scanner) {
            while(system) {
                System.out.println("Qual acao de cores deseja executar");
                System.out.println("0 - Sair");
                System.out.println("1 - Salvar");
                System.out.println("2 - Atualizar");
                System.out.println("3 - Visualizar");
                System.out.println("4 - Deletar");
    
                int action = scanner.nextInt();
    
                switch (action) {
                    case 1:
                        salvar(scanner);
                        break;
                    case 2:
                        atualizar(scanner);
                        break;
                    case 3:
                        visualizar();
                        break;
                    case 4:
                        deletar(scanner);
                        break;
                    default:
                        system = false;
                        break;
                }
    
            }
    
        }
    
        private void salvar(Scanner scanner) {
            String nome = scanner.next();
            Cores cores = new Cores();
            cores.setNome(nome);
            coresRepository.save(nome);
            System.out.println("Salvo");
        }
    
        private void atualizar(Scanner scanner) {
            System.out.println("Id");
            int id = scanner.nextInt();
            String nome = scanner.next();
    
            Cores cores = new Cores();
            cores.setId(id);
            cores.setNome(nome);
            coresRepository.save(nome);
            System.out.println("Atualizado");
        }
    
        private void visualizar() {
            Iterable<Cores> cores = coresRepository.findAll();
            cores.forEach(cores -> System.out.println(cores));
        }
    
        private void deletar(Scanner scanner) {
            int id = scanner.nextInt();
            coresRepository.deleteById(id);
            System.out.println("Deletado");
        }
    }
    ````

    

21. Explique como funciona as `Derived Query` e de exemplos de métodos com o uso das opções: `E` , `OU` e `MAIOR QUE`.

    - Derived Query -> Através de um comando, de um método escrito em Java, o próprio Spring Data consegue pegar método e criar uma Query para o seu banco de dados específico sem você ter que criar nenhum comando SQL. Para que o Spring Data consiga entender tem que passar o comando "findByCampoParaConsulta". Quando colocamos FindBy significa que nós queremos fazer uma consulta por alguma coisa.

    - ````java
      public interface CoresRepository extends CrudRepository<Cores, Integer> {
          List<Funcionario> findById_corAndNome(int id_cor, String nome);
          List<Funcionario> findById_corOrNome(int id_cor, String nome);
          List<Funcionario> findByAgeGreaterThan(String nome);
      }
      ````

      

22. Explique qual a aplicabilidade ideal para os Repository `CrudRepository`, `PaginAndSortingRepository` e `JpaRepository`. Escreva a forma de como declarar a assinatura de cada um deles.

    - CrudRepository -> Fornece principalmente funções CRUD.

    - ````java
      public interface CoresRepository extends CrudRepository<Cores, Integer> {
          List<Cores> findByNome(String nome);
      }
      ````

      

    - PaginAndSortingRepository -> Fornece métodos para fazer paginação e classificação de registros.

    - ````java
      public interface CoresRepository extends PaginAndSortingRepository <Cores, Integer> {
          List<Cores> findByNome(String nome);
      }
      ````

      

    - JpaRepository -> Fornece alguns métodos relacionados ao JPA, como liberar o contexto de persistência e excluir registros em um lote.

    - ````java
      public interface CoresRepository extends JpaRepository<Cores, Long> {
          List<Cores> findByNome(String nome);
      }
      ````

23. Dado o código abaixo:

```SQL
   Create Table Cores(
      ID_COR INT auto_increment,
      NOME VARCHAR(50),
      PRIMARY KEY(ID_COR)
   );

   Create Table Carros(
      ID_CARRO INT auto_increment,
      MARCA VARCHAR(50),      
      MODELO VARCHAR(50),
      PLACA VARCHAR(10),
      ID_COR INT,
      PRIMARY KEY(ID_CARRO)
   );

   ALTER TABLE Carros ADD FOREIGN KEY(ID_COR) REFERENCES Cores (ID_COR);
```
  Escreva um exemplo de método que use uma `projeção` que exiba os dados dos carros com cor azul, os campos exibidos devem ser:
     Marca, Modelo, Placa e nome da cor

- ````java
  public List<Tuple> buscarTodos() {  
      String query = "SELECT carros.marca AS marca, carros.modelo AS modelo, carros.placa AS placa, cores.nome AS cor FROM Carros carros JOIN carros.cores cores"; 
      entityManager = JPAUtil.getEntityManager();  
      List<Tuple> result = entityManager
  	        .createQuery(query, Tuple.class)
  	        .getResultList();  
      entityManager.close();  
      return result;  
  }
  ````

24. Explique para que servem as anotações `@Repository`, `@Service`, `@Controller`, `@SpringBootApplication`

    - @Repository -> Anotação que serve para definir uma classe como pertencente à camada de persistência.

    - @Service -> Anotação que serve para definir uma classe como pertencente à camada de Serviço da aplicação.

    - @Controller -> Essa "annotation" é usada para criar uma classe como "controller", que pode manipular solicitações "requests" do cliente e enviar "response".

    - @SpringBootApplication -> Éssa "annotation" poderá executar a aplicação sem a necessidade de instalar um servidorWeb, pois ele vem com um servidor Tomcat incorporado.

      

25. Escreva uma classe que contenha um método REST que retorna uma lista fixa de Carros, utilize o padrão DTO.

    ````java
    //package and import 
    
    @RestController
    public class NomeClassController {
        
        @RequestMapping(value="/carros")
        public List<Carros> lista() {
    		Carros carros = new Carros("marca", "modelo", "placa", 1); 
            return CarrosDto.converter(Arrays.asList(carros));
        }
    }
    ````

    ````java
    //package and import 
    
    public class CarrosDto {
        private int id_carro;
        private String marca;
        private String modelo;
        
        public CarrosDto(Carros carros) {
            this.id_carro = carros.getId_carro();
        	this.marca = carros.getMarca();
        	this.modelo =  carros.getModelo;
        }
        
        //Getter
        
        public static List<Carros> converter(List<Carros> carros) {
            return carros.stream().map(CarrosDto::new).collect(Collectors.toList());
        }
    }
    ````

    

26. Adapte seu código da atividade anterior para um modelo que usa o padrão `Repository` e retorne uma lista de Carros. 

    - ````java
      //package and import 
      
      @RestController
      public class NomeClassController {
          
          @Autowired
          private CarroRepository carroRepository;
          
          @RequestMapping(value="/carros")
          public List<Carros> lista() { 
              List<Carros> carros = carroRepository.findAll();
              return CarrosDto.converter(carros);
          }
      }
      ````

      

    - ````java
      //package and import 
      
      public interface CarroRepository JpaRepository<Carros, Integer>{   
      }
      ````

      

27. Qual a finalidade do `Bean Validation`, Explique e de um exemplo do seu uso em código com a annotation `@Valid`  

    - Bean Validation -> É uma especificação que permite validar objetos com facilidade em diferentes camadas da aplicação. A vantagem de usar Bean Validation é que as restrições ficam inseridas nas classes de modelo.

      ````java
      //package and import
       
      public class Cliente {
       
        @NotNull
        @Size(min = 3, max = 20)
        private String nome;
       
        @NotNull
        @Size(min = 3, max = 40)
        private String sobrenome;
       
        // getters e setters
       
      }
      ````

      

28. Explique para que é usado a annotation `@ExceotionHandler` e de um exemplo de método que utiliza ela.

    - @ExceotionHandler -> Anotação usada para tratar exceções em classes.

      ````java
      @ExceptionHandler(NullPointerException.class)
      public void handleException(NullPointerException ex){
      }
      ````

      
