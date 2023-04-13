#Imports

###com autenticação
```zsh
  pip install fastapi uvicorn sqlalchemy asyncpg psycopg2-binary gunicorn python-jose[cryptography] python-multipart bcrypt pytz passlib
```

#Core
##Settings
```py
  from typing import List
  from pydantic import BaseSettings
  from sqlalchemy.ext.declarative import declarative_base
  from os import getenv

  class Settings(BaseSettings):
      API_V1_STR: str = '/api/v1'
      DB_URL: str = 'postgresql+asyncpg://lenner:mg1qD7C28e4xrr73qb6p2SZKC33xS4Va@dpg-cggu2kseoogqfc4kag10-a/faculdade'
      DBBaseModel = declarative_base()

      JWT_SECRET: str = getenv('JWT_SECRET')
      ALGORITHM: str = 'HS256'
      ACESS_TOKEN_EXPIRE_MINUTES: int = 60 * 24 * 7

      class Config:
          case_sensitive = True

  settings: Settings = Settings()
```

##database
```py
  from sqlalchemy.orm import sessionmaker
  from sqlalchemy.ext.asyncio import create_async_engine, AsyncEngine, AsyncSession

  from core.configs import settings

  engine: AsyncEngine = create_async_engine(settings.DB_URL)

  Session: AsyncSession = sessionmaker(
      autocommit=False,
      autoflush=False,
      expire_on_commit=False,
      class_=AsyncSession,
      bind=engine
  )
```

##deps
```py
from typing import Generator
from fastapi import Depends, HTTPException, status
from jose import jwt, JWTError

from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy.future import select
from pydantic import BaseModel
from typing import Optional
from core.database import Session
from core.auth import oauth2_schema
from core.configs import settings
from models.usuario_model import UsuarioModel


class TokenData(BaseModel):
    username: Optional[str]= None

async def get_session() -> Generator:
    session: AsyncSession = Session()

    try:
        yield session
    finally:
        await session.close()

async def get_current_user(
    db: Session = Depends(get_session), 
    token: str = Depends(oauth2_schema)
) -> UsuarioModel:
    credential_exception: HTTPException = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED, 
        detail='Não foi possível autenticar a credencial',
        headers={'WWW-Authenticate': 'Bearer'}
    )

    try:
        payload = jwt.decode(
            token,
            settings.JWT_SECRET,
            algorithms=[settings.ALGORITHM],
            options={'verify_aud': False}
        )

        username: str = payload.get('sub')
        if username is None:
            raise credential_exception
        
        token_data: TokenData = TokenData(username=username)

    except JWTError:
        raise credential_exception

    async with db as session:
        query = select(UsuarioModel).filter(UsuarioModel.id == int(token_data.username))
        result = await session.execute(query)
        usuario: UsuarioModel = result.scalars().unique().one_or_none()

        if usuario is None:
            raise credential_exception

        return usuario
```

##security
```py
  from passlib.context import CryptContext

  CRIPTO = CryptContext(schemes=['bcrypt'], deprecated='auto')

  def verificar_senha(senha: str, hash_senha:str) -> bool:
      return CRIPTO.verify(senha, hash_senha)

  def gerar_hash_senha(senha: str) -> str:
      return CRIPTO.hash(senha)
```

##auth
```py
  from typing import List

  from pytz import timezone
  from datetime import datetime, timedelta

  from fastapi.security import OAuth2PasswordBearer

  from sqlalchemy.future import select
  from sqlalchemy.ext.asyncio import AsyncSession

  from jose import jwt

  from models.usuario_model import UsuarioModel
  from core.configs import settings
  from core.security import verificar_senha
  from typing import Optional

  from pydantic import EmailStr

  oauth2_schema = OAuth2PasswordBearer(
      tokenUrl=f"{settings.API_V1_STR}/usuarios/login",
  )

  async def autenticar(email: EmailStr, senha: str, db: AsyncSession) -> Optional[UsuarioModel]:
      async with db as session:
          query = select(UsuarioModel).filter(UsuarioModel.email == email)
          result = await session.execute(query)
          usuario: UsuarioModel = result.scalars().unique().one_or_none()

          if not usuario:
              return None

          if not verificar_senha(senha, usuario.senha):
              return None
          
          return usuario

  def _criar_token(tipo_token: str, tempo_vida: timedelta, sub: str) -> str:
      payload = {}
      sp = timezone('America/Sao_Paulo')
      expira = datetime.now(tz=sp) + tempo_vida

      payload['type'] = tipo_token
      payload['exp'] = expira
      #iat = issued at = emitido em
      payload['iat'] = datetime.now(tz=sp)
      payload['sub'] = str(sub)

      return jwt.encode(payload, settings.JWT_SECRET, algorithm=settings.ALGORITHM)

  def criar_token_acesso(sub: str) -> str:

      return _criar_token(
          tipo_token='acess_token',
          tempo_vida=timedelta(minutes=settings.ACESS_TOKEN_EXPIRE_MINUTES),
          sub=sub,
      )
```

#Criar tabelas
```py
  from core.configs import settings
  from core.database import engine

  async def create_tables() -> None:
      import models.__all_models
      print('Criando as tabelas no banco de dados')

      async with engine.begin() as conn:
          await conn.run_sync(settings.DBBaseModel.metadata.drop_all)
          await conn.run_sync(settings.DBBaseModel.metadata.create_all)
      print('tabelas criadas com sucesso')

  if __name__ == '__main__':
      import asyncio

      asyncio.run(create_tables())
```

#Endpoints usuário

###logado
```py
  # GET LOGADO
  @router.get('/logado', response_model=UsuarioSchemaBase)
  def get_logado(usuario_logado: UsuarioModel = Depends(get_current_user)):
      return usuario_logado
```

###cadastrar
```py
  # POST / SIGNUP
  @router.post('/signup', status_code=status.HTTP_201_CREATED, response_model=UsuarioSchemaBase)
  async def post_usuario(usuario: UsuarioSchemaCreate, db: AsyncSession = Depends(get_session)):
      novo_usuario: UsuarioModel = UsuarioModel(
          nome=usuario.nome,
          sobrenome=usuario.sobrenome,
          email=usuario.email,
          senha= gerar_hash_senha(usuario.senha),
          eh_admin=usuario.eh_admin,
      )
      async with db as session:
          try:
              session.add(novo_usuario)
              await session.commit()

              return novo_usuario
          except IntegrityError:
              raise HTTPException(status_code=status.HTTP_406_NOT_ACCEPTABLE, detail="Já existe um usuario com este email cadastradado")
```

###Ver todos os usuarios
```py
  # GET Usuarios
  @router.get('/', status_code=status.HTTP_200_OK, response_model=List[UsuarioSchemaBase])
  async def get_usuarios(db: AsyncSession = Depends(get_session)):
      async with db as session:
          query = select(UsuarioModel)
          result = await session.execute(query)
          usuarios: List[UsuarioSchemaBase] = result.scalars().unique().all()
          
          return usuarios
```

###Ver um usuario
```py
  # GET USUARIO         
  @router.get('/{usuario_id}', status_code=status.HTTP_200_OK, response_model=UsuarioSchemaArtigos)
  async def get_usuario(usuario_id: int, db: AsyncSession = Depends(get_session)):
      async with db as session:
          query = select(UsuarioModel).where(UsuarioModel.id == usuario_id)
          result = await session.execute(query)
          usuario: UsuarioSchemaBase = result.scalar()

          if usuario is None:
              raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Usuario não encontrado")

          return usuario
```

###fazer login
```py
  # POST LOGIN
  @router.post('/login')
  async def login(form_data: OAuth2PasswordRequestForm = Depends(), db: AsyncSession = Depends(get_session)):
      usuario = await autenticar(email=form_data.username, senha=form_data.password, db=db)

      if usuario is None:
          raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail='Dados de acesso incorretos')

      return JSONResponse(content={
          "acess_token": criar_token_acesso(sub=usuario.id),
          "token_type": "bearer",
      }, status_code=status.HTTP_200_OK)
```